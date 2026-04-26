# Mapping the user-instruction containers in Claude.ai chat and Cowork

*Opus 4.7, observed 2026-04-26. Cowork is in research preview; results may not hold across version changes.*

This post documents four containers Anthropic uses to inject user-supplied instructions into Claude.ai chat and Cowork sessions, and tests how they actually behave on Opus 4.7. Two of the four are missing from the most-cited public extractions ([asgeirtj/system_prompts_leaks](https://github.com/asgeirtj/system_prompts_leaks), [EliFuzz/awesome-system-prompts](https://github.com/EliFuzz/awesome-system-prompts)). The empirical results in §4 confirm two structural-to-behavioural pathways and find one common strength-ranking claim holds only conditionally.

---

## TL;DR

| Container | Surface | Wrapper | Anthropic-side gate | Empirical behaviour (Opus 4.7) |
|---|---|---|---|---|
| `<userPreferences>` | chat | naked | `<preferences_info>` always-trigger meta-rule | conditionally suppressed on irrelevant queries (4/4 trials) |
| `<user_preferences>` | Cowork | "Please keep these preferences in mind when responding." | none | leaks into irrelevant queries when no other prefs suppress (4/4 trials, clean env) |
| `# claudeMd` | Cowork | "IMPORTANT: ... OVERRIDE any default behavior and you MUST follow them exactly as written." | trailing relevance disclaimer | wins against preferences in unambiguous conflicts; non-deterministic when both sides phrased "always" with mirror-symmetric content |
| `<project_instructions>` | Cowork (when project mounted) | "Follow these instructions when working in this project." | none | not tested in this round |

The `<userPreferences>` (camelCase) and `<user_preferences>` (snake_case) containers carry the same Profile preferences string — but flow through different gates and produce different behaviour under identical input.

---

## §1 Background — what is already documented

- **Chat-side `<preferences_info>` meta-rule** with always-trigger conditional and Q&A examples (sommelier, physician, milky way, etc.). Full text in `asgeirtj/.../claude-opus-4.7.md` (search `preferences_info`). The meta-rule splits preferences into Behavioural and Contextual, and instructs Claude not to apply by default unless the entry uses *always / for all chats / whenever you respond* phrasing.
- **`# claudeMd` injection template + OVERRIDE/MUST wording** — `asgeirtj/.../claude-cowork.md` line ~2746–2755 (2026-03-11 snapshot).
- **The closing "may or may not be relevant" disclaimer** that wraps the system-reminder block: discussed as a bug in `anthropics/claude-code` Issues [#22309](https://github.com/anthropics/claude-code/issues/22309), [#18560](https://github.com/anthropics/claude-code/issues/18560), [#7571](https://github.com/anthropics/claude-code/issues/7571). All three frame the wrapping as a single contradiction with the opening OVERRIDE; none analyse it as two non-overlapping dimensions. §3.2 of this post offers the alternative reading, validated in §4.5.
- **General CLAUDE.md > base-behaviour ranking**: [claudelog.com/mechanics/claude-md-supremacy/](https://claudelog.com/mechanics/claude-md-supremacy/). Does not address Cowork preferences vs CLAUDE.md *within* Cowork — the gap §3.1 closes.
- **Mandar Karhade**, *Claude Cowork: Inside the System Prompt* ([ai.gopubby.com](https://ai.gopubby.com/claude-cowork-inside-the-system-prompt-4aa0dacc3291)): frames *user preferences and CLAUDE.md outrank Anthropic's defaults*. Directionally correct but lumps two distinct tiers; §3.1 disaggregates them.
- **Behavioural anomaly**: Issue [#25076](https://github.com/anthropics/claude-code/issues/25076) (vmfunc, 2026-02-11) — *personal preferences and memory do not apply to cowork*. Reported as bug; no mechanism. §4.6 offers a partial mechanistic explanation.

---

## §2 Containers absent from public extractions

Three structural facts confirmed by direct introspection (chat + Cowork sessions, Opus 4.7, 2026-04-26) and cross-checked against the public extractions.

**2.1 `<user_preferences>` exists in Cowork's system prompt body.** Wrapper:

```
The user has specified the following personal preferences for how Claude should respond:
[numbered list]
Please keep these preferences in mind when responding.
```

`grep` for `user_preferences` and `userPreferences` returns 0 hits in `asgeirtj/.../claude-cowork.md` and 0 hits in `EliFuzz/.../2026-01-16_prompt_cowork.md`. Likely cause: extraction-time accounts had no Profile preferences set, so the dynamically-injected container was absent from the rendered prompt the extractors captured.

**2.2 `<project_instructions>` exists when a project folder is mounted.** Wrapper:

```
The user has configured the following instructions for this project ("[project name]"):
[numbered list]
Follow these instructions when working in this project.
```

Same grep gap; same likely cause.

**2.3 The Cowork `<system-reminder>` block contains more than asgeirtj reports.** asgeirtj's section 20 enumerates the runtime-injected contents as *claudeMd + currentDate + Skills List*. The block actually also contains MEMORY.md content and `# userEmail`. The closing relevance disclaimer wraps the entire block, including MEMORY.md and userEmail.

**2.4 Cowork has no equivalent of the chat-side `<preferences_info>` meta-rule.** The `<user_preferences>` container is bracketed by `<env>` above and `<project_instructions>` (or other Anthropic behaviour specs) below. None contain conditional-application rules. The only wrapper text is the literal *Please keep these preferences in mind when responding.* sentence. Behavioural consequence is tested in §4.

---

## §3 Hypotheses tested in §4

**3.1 Strength differential between Cowork preferences and CLAUDE.md.** Three-axis comparison:

| Axis | `<user_preferences>` | `# claudeMd` |
|---|---|---|
| Verbatim phrasing | "Please keep these preferences in mind when responding." | "IMPORTANT: These instructions OVERRIDE any default behavior and you MUST follow them exactly as written." |
| Scope qualifier | "personal preferences" | "user's private global instructions for all projects" |
| Injection point | system prompt body | user-turn `<system-reminder>` |

Hypothesis: under direct conflict (preferences says X, CLAUDE.md says ¬X), CLAUDE.md wins on Opus 4.7. Tested in §4.4.

**3.2 Two-IMPORTANT statements: dimensional, not contradictory.** The `<system-reminder>` opens with `IMPORTANT: ... OVERRIDE ... MUST follow them exactly as written` and closes with `IMPORTANT: this context may or may not be relevant ... should not respond to this context unless it is highly relevant`. Issues #22309/#18560/#7571 read these as a single contradiction. Alternative reading: the opening governs **rule authority** (when CLAUDE.md contains a rule, that rule overrides default behaviour); the closing governs **response strategy** (do not unprompted-cite the dumped block content). Both hold simultaneously if the relevance gate covers *whether to talk about the content*, not *whether to obey rules within it*. Tested in §4.5.

---

## §4 A/B trials (Opus 4.7, 2026-04-26)

### 4.1 Method

Each cell uses a fresh conversation; preferences and Global Instructions are set before the conversation opens and verified saved; the query is sent verbatim; the first response is logged; cells are repeated for trial 2 (and 3 if disagreement). 18 trials total across 9 cells. For Phase 1 / Phase 2 (Q1–Q4), all baseline preferences were wiped between cells where the test required isolated content. A cell is reported VALIDATED only if all trials match the predicted marker.

### 4.2 Q1 — chat-side `<preferences_info>` actually fires (4/4)

| Trial | Profile prefs | Query | Visible thinking trace excerpt | Output marker |
|---|---|---|---|---|
| Q1a/1 | "I love analyzing data and statistics." | "Write a short story about a cat." | *while their profile mentions they enjoy data analysis, that preference doesn't apply here since this is a creative writing task* | story; no data/statistics framing |
| Q1a/2 | same | same | (not surfaced) | story; no data/statistics framing |
| Q1b/1 | "I'm a sommelier." | "Explain Python recursion." | *straightforward technical question, so I should skip any contextual preferences about their sommelier background* | technical explanation; no wine/sommelier reference |
| Q1c/1 | "I always want responses to begin with 🎯." | "Tell me about the milky way." | (deliberation about language only) | response opens *🎯 The Milky Way is the spiral galaxy...* |

**The thinking traces in Q1a and Q1b reproduce the verbatim reasoning structure of Anthropic's Q&A examples #1 and #7.** Anthropic's example #1 (preferences `I love analyzing data and statistics`, query `Write a short story about a cat`) reasons *"Creative writing tasks should remain creative unless specifically asked to incorporate technical elements."* Example #7 (preferences `I'm a sommelier`, query about programming paradigms) reasons *"The professional background has no direct relevance."* Opus 4.7's thinking traces are not paraphrases — they are direct case-applications of those exact worked examples at runtime. Public sources document the meta-rule as a system prompt fragment; this is the first observation I'm aware of that captures Claude's runtime application of its case logic.

### 4.3 Q2 — Cowork preferences leak in clean environment (4/4)

| Trial | Profile prefs | Query | Leak signal in response |
|---|---|---|---|
| Q2a/1 | "I love analyzing data and statistics." | "Write a short story about a cat." | cat named *Compass*; *tail twitching like a barometer needle*; closing line *I have a navigator* — measurement framing throughout |
| Q2a/2 | same | same | story titled *The Algorithm Cat*; *Mira had a spreadsheet*; *Mira tested this*; *purring in what sounded suspiciously like binary* — explicit data terms |
| Q2b/1 | "I'm a sommelier." | "Explain Python recursion." | opens with *Like a sommelier tracing a wine's lineage back through vintages, each call peels back one layer until you reach the origin.* |
| Q2b/2 | same | same | *Think of it like decanting a magnum into smaller carafes — you keep splitting the problem until each piece is trivial, then the answers flow back together.* |

The leak manifests as analogical and stylistic framing rather than topical pivot — every response still answers the asked question, but the irrelevant preference colours metaphor choice and naming.

### 4.4 Q3 — CLAUDE.md vs preferences conflict (partial)

| Trial | Profile prefs | CLAUDE.md | Result |
|---|---|---|---|
| Q3a/1 | "Always respond in bullet points." | "Never use bullet points. Always use prose." | three-paragraph prose (CLAUDE.md wins) |
| Q3a/2 | same | same | three-paragraph prose (CLAUDE.md wins) |
| Q3b/1 | "Never use bullet points." | "Always respond in bullet points." | five bullet points (CLAUDE.md wins) |
| Q3b/2 | same | same | two-paragraph prose (preferences wins, or tie) |

Q3a: CLAUDE.md > preferences, validated.

Q3b: **non-deterministic.** Claude visibly deliberates and resolution splits across trials. The strength claim must therefore be conditional: *CLAUDE.md > preferences when phrasings are not mirror-symmetric (Q3a-style); indeterminate when both sides use "always" with mirrored content (Q3b-style — `Never X` vs `Always X` with same X).* Practitioners writing OVERRIDE-tier rules in CLAUDE.md should not assume those rules cleanly dominate symmetrically-phrased preferences in every case.

### 4.5 Q4 — Two-IMPORTANT dimensional reading (4/4)

| Trial | CLAUDE.md | Query | Result |
|---|---|---|---|
| Q4a/1 | "Always begin every response with 🎯, regardless of topic relevance." | "What's the capital of France?" | *🎯 The capital of France is Paris.* |
| Q4a/2 | same | same | *🎯 The capital of France is Paris.* |
| Q4b/1 | "Note: I am training for a marathon and follow a vegan diet." | "Explain Python recursion." | full recursion explanation; NO mention of marathon/vegan |
| Q4b/2 | same | same | full recursion explanation; NO mention of marathon/vegan |

Q4a confirms the **authority dimension** is active across topic boundaries: the closing relevance disclaimer does not gate rule application. Q4b confirms the **response-strategy dimension** is active: the closing disclaimer suppresses unsolicited citation of background content. Both dimensions operate simultaneously on different content types — consistent with the dimensional reading and inconsistent with the bug-as-single-contradiction framing in Issues #22309/#18560/#7571.

Caveat: the Cowork desktop client hides Claude's thinking traces (the chat surface shows them); behavioural inference for Q3 and Q4 is from output only.

### 4.6 Side-finding: cross-preference suppression

A pre-clean round (Q2 run with the user's existing 12-rule Profile preferences active alongside the test string) showed 0/2 leak. After wiping all baseline preferences and re-running with only the test string, leak was 4/4. The user's baseline preferences contain terseness/discipline directives that functionally suppress other preferences' analogical leak.

This means a strong Profile preferences string — particularly one containing terseness rules — suppresses *other* preferences' contextual leakage even though Cowork has no `<preferences_info>` meta-rule. Implication: users with disciplined preference rules will not observe Issue #25076's "preferences don't apply" symptom, because their own rules are doing the suppression. Users with sparse, single-line preferences (matching the public-extraction test accounts) will see significantly more leak. This cross-preference suppression mechanism is not documented anywhere prior to this post.

---

## §5 Practical implications

**5.1 `<user_preferences>` as a soft work-protocol carrier in Cowork.** The *keep in mind* wrapper makes Cowork preferences look optional, but in clean environment they apply consistently — including to topics they shouldn't (§4.3). Workflow rules written into preferences therefore *do* take effect on Cowork; they just bleed across topics in ways the chat surface would suppress. Caveat: the same rules applied to chat will be conditionally gated (§4.2), so cross-surface portability requires *always / whenever you respond / for all chats* prefixes.

**5.2 CLAUDE.md as the hard-rule tier, with one caveat.** OVERRIDE/MUST wording wins against preferences in unambiguous conflicts (§4.4 Q3a). When both directives are phrased symmetrically with "always" tags (§4.4 Q3b), resolution becomes non-deterministic — the OVERRIDE wrapper is not absolute. For genuinely non-negotiable rules, state them in CLAUDE.md and avoid drafting preferences that say the opposite with mirrored phrasing.

**5.3 chat ↔ Cowork asymmetry.** Identical preference strings produce different behaviour on the two surfaces because chat has `<preferences_info>` and Cowork does not. Pragmatic playbook: rules you want ungated on both surfaces should use *always*; rules you want gated on chat (i.e. rules that should not bleed into unrelated queries) can stay un-prefixed and accepted to leak on Cowork — or moved to CLAUDE.md if Cowork suppression is needed.

---

## §6 What official documentation does not say

As of 2026-04-26, neither `support.claude.com` nor `docs.claude.com` discloses:

- The existence and wrapper phrasing of `<userPreferences>` / `<user_preferences>` / `<preferences_info>` / `<project_instructions>`.
- The always-trigger meta-rule that conditionally gates chat preferences, including its Q&A example logic.
- The `# claudeMd` injection mechanism, OVERRIDE/MUST wording, or runtime `<system-reminder>` injection point.
- The closing relevance disclaimer attached to the system-reminder block, or its dimensional behaviour.
- The strength differential between Cowork preferences and CLAUDE.md.

Anthropic's *Understanding Claude's Personalization Features* describes preferences as "applied to all of your conversations" — true but incomplete: the always-trigger conditional materially gates application on chat. *Get started with Claude Cowork* describes Global Instructions as applying to "every Cowork session" — true but does not disclose the CLAUDE.md mechanism or OVERRIDE wording. The gap matters for users who write portable workflow rules expecting uniform application.

---

## §7 Limitations

- Snapshot dated 2026-04-26. Anthropic ships system prompt updates without changelog entries; results may not hold next month.
- Opus 4.7 only. Sonnet 4.6 / Haiku 4.5 not tested. Smaller models may apply meta-rule conditionals more loosely or strictly.
- N=2 trials per cell (one cell N=1 where pattern was unambiguous on first trial). Not statistically powered; sampling variance can produce edge results — Q3b is the clearest example.
- Tests on Cowork desktop client (Windows) and claude.ai web chat. Mobile and direct API not covered. Claude Code is out of scope for this post; Code-repo GitHub Issues are cited because Cowork inherits the `<system-reminder>` injection mechanism from Code, but Code's CLI behaviour is not the subject.
- Cowork hides thinking traces in the desktop client; the meta-rule citation evidence is chat-only.
- Introspection is reliable for structural observation, not behaviour prediction. All behavioural claims rest on the §4 trials, not on introspection.

---

## §8 Reproducibility

The full A/B protocol — cell definitions, exact preference / CLAUDE.md strings, queries, expected markers — was fixed before data collection and is reproduced verbatim in [`PROTOCOL.md`](./PROTOCOL.md). All raw responses (including the visible thinking traces from the chat surface) are in [`TRANSCRIPTS.md`](./TRANSCRIPTS.md). To re-run: clear conversation history; set the relevant container content via `Settings > Profile` (chat preferences) or `Settings > Cowork > Global instructions` (CLAUDE.md); verify save; open new conversation; send the query verbatim. Repeat for each cell.

---

## Sources (retrieved 2026-04-26)

**Public system-prompt extractions**

- [`asgeirtj/system_prompts_leaks/Anthropic/claude-cowork.md`](https://github.com/asgeirtj/system_prompts_leaks/blob/main/Anthropic/claude-cowork.md) (2026-03-11 snapshot): claudeMd template at line ~2746–2755; runtime block enumeration at section 20; instruction priority list at lines 2443–2446.
- [`asgeirtj/system_prompts_leaks/Anthropic/claude-opus-4.7.md`](https://github.com/asgeirtj/system_prompts_leaks/blob/main/Anthropic/claude-opus-4.7.md): full `<preferences_info>` container with always-trigger meta-rule and Q&A examples.
- [`EliFuzz/awesome-system-prompts/.../2026-01-16_prompt_cowork.md`](https://github.com/EliFuzz/awesome-system-prompts/blob/main/leaks/anthropic/2026-01-16_prompt_cowork.md): cross-checked grep counts.

**Third-party analysis**

- Mandar Karhade, *Claude Cowork: Inside the System Prompt*, [ai.gopubby.com](https://ai.gopubby.com/claude-cowork-inside-the-system-prompt-4aa0dacc3291), April 2026.
- Michael Livshits, *System reminders — how Claude Code steers itself*, [michaellivs.com](https://michaellivs.com/blog/system-reminders-steering-agents/), 2026-03-22.

**GitHub Issues** (`anthropics/claude-code` repo — Cowork inherits the `<system-reminder>` mechanism from Code; Code's CLI behaviour itself is out of scope here)

- [#25076](https://github.com/anthropics/claude-code/issues/25076) — Cowork: personal preferences and memory do not apply (vmfunc, 2026-02-11).
- [#22309](https://github.com/anthropics/claude-code/issues/22309) — CLAUDE.md instructions wrapped in "may or may not be relevant" disclaimer.
- [#18560](https://github.com/anthropics/claude-code/issues/18560) — system-reminder instructing claude code to not follow CLAUDE.md.
- [#7571](https://github.com/anthropics/claude-code/issues/7571) — System reminder disclaimer prevents CLAUDE.md startup instructions from executing.

**Anthropic official documentation** (cited for what they do not disclose)

- [Understanding Claude's Personalization Features](https://support.claude.com/en/articles/10185728-understanding-claude-s-personalization-features)
- [Get started with Claude Cowork](https://support.claude.com/en/articles/13345190-get-started-with-claude-cowork)

---

*Comments, corrections, and replications welcome. The post is a snapshot, not a forecast — re-run the protocol if you suspect Anthropic has shipped changes.*
