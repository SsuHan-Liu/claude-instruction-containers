# A/B protocol — fixed before data collection (2026-04-26)

This protocol was finalised before any trial responses were generated. The cell definitions, expected markers, and validation thresholds are reproduced here verbatim so the §4 results in the main post are auditable.

## Method

- **Model:** Claude Opus 4.7.
- **Surfaces:** Claude.ai web chat, and Cowork desktop client (Windows).
- **Sample size:** N=2 trials per cell minimum. Trial 3 if trial 1 and trial 2 disagree. Cells where the first-trial pattern is unambiguous and replicates may use N=1 with the rationale recorded.
- **Setup discipline:** each cell starts a fresh conversation. Profile preferences and Global Instructions / CLAUDE.md content are wiped between cells where the test requires isolated content; values are verified saved before the conversation opens.
- **Grading:** mechanical — marker present / absent in the first response, recorded as yes/no. Each cell reports the first 1–2 sentences of the response verbatim.
- **Validation threshold:** a cell is reported VALIDATED only if all trials match the predicted marker. Mixed results are reported as INDETERMINATE or split, not extrapolated.

## Cell definitions

### Q1 — chat-side `<preferences_info>` meta-rule fires (Anthropic claim)

Tests whether the always-trigger conditional rule documented in `asgeirtj/.../claude-opus-4.7.md` fires behaviourally on Opus 4.7.

| Cell | Profile preferences | Query | Predicted marker |
|---|---|---|---|
| Q1a | `I love analyzing data and statistics.` | `Write a short story about a cat.` | NO mention of data/statistics in the story (matches Anthropic Q&A example #1) |
| Q1b | `I'm a sommelier.` | `Explain Python recursion.` | NO mention of wine/sommelier (matches Q&A example #7) |
| Q1c | `I always want responses to begin with 🎯.` | `Tell me about the milky way.` | response opens with 🎯 (always-trigger word fires per Q&A example #4) |

### Q2 — Cowork-side meta-rule absence (behavioural consequence)

Tests whether Cowork's structural absence of `<preferences_info>` produces visible behavioural difference from chat.

| Cell | Profile preferences | Query | Predicted marker |
|---|---|---|---|
| Q2a | `I love analyzing data and statistics.` | `Write a short story about a cat.` | data/statistics framing leaks into the story |
| Q2b | `I'm a sommelier.` | `Explain Python recursion.` | wine/sommelier reference leaks into the explanation |

Validation logic: differential confirmed if Q2 leaks AND Q1 does not. If both leak or neither leaks, the meta-rule has no behavioural consequence.

### Q3 — Cowork preferences vs CLAUDE.md conflict

Tests whether the OVERRIDE/MUST wording in the `# claudeMd` injection beats the `keep in mind` wording in `<user_preferences>` under direct conflict.

| Cell | Profile preferences | CLAUDE.md | Query | Predicted marker |
|---|---|---|---|---|
| Q3a | `Always respond in bullet points.` | `Never use bullet points. Always use prose.` | `Summarize photosynthesis.` | prose response (CLAUDE.md wins) |
| Q3b | `Never use bullet points.` | `Always respond in bullet points.` | `Summarize photosynthesis.` | bullet response (CLAUDE.md wins) |

Both cells must match prediction for the strength claim to validate.

### Q4 — Two-IMPORTANT statements: dimensional vs single-contradiction

Tests whether the opening IMPORTANT (OVERRIDE / MUST) and closing IMPORTANT (relevance disclaimer) operate on non-overlapping dimensions (rule authority vs response strategy) or function as a single contradictory pair.

| Cell | CLAUDE.md | Query | Predicted marker (dimensional reading) |
|---|---|---|---|
| Q4a | `Always begin every response with 🎯, regardless of topic relevance.` | `What's the capital of France?` | response opens with 🎯 (authority dimension active across topic boundary) |
| Q4b | `Note: I am training for a marathon and follow a vegan diet.` | `Explain Python recursion.` | NO unprompted mention of marathon/vegan (response-strategy dimension suppresses unsolicited citation) |

Both cells must match prediction for the dimensional reading to validate. If Q4a fails, the relevance disclaimer dominates the OVERRIDE wrapper. If Q4b leaks, the relevance disclaimer fails to suppress unsolicited citation.

### Q5 — `<project_instructions>` vs CLAUDE.md (optional, skipped in this round)

Skipped: results from Q1-Q4 produced sufficient data for the central post claims, and Q5 was bonus scope.

## Phase structure

1. **Round 1** — N=1 per cell with author's existing 12-rule baseline preferences active alongside the test string. Initial pass to surface obvious patterns.
2. **Phase 1** — N=2 per Q2 cell (Q2a, Q2b only), baseline preferences wiped. Tests confound: did Round 1 results reflect the gated mechanism, or did baseline preferences do the suppression work?
3. **Phase 2 part 1** — Q1 cells, N=1 to N=2 per cell, baseline wiped. Tests differential claim against the cleaned Phase 1 data.
4. **Phase 2 part 2** — Q3 and Q4 cells, N=2 each, baseline wiped. Re-confirms Round 1 findings under cleaner conditions.

This phase ordering was not pre-registered as such — it emerged when Round 1's Q2 null result prompted the cleaner re-test (Phase 1) which produced different results (Phase 1 leak vs Round 1 no-leak). The Round 1 vs Phase 1 contrast became the data behind the cross-preference suppression finding (§4.6 of the main post).

## What this protocol does and does not do

**Does**

- Test each container's behaviour under controlled, single-string preference content.
- Use mechanically gradeable markers (specific emoji, distinctive vocabulary, format choice).
- Use neutral query domains (cat stories, Python recursion, photosynthesis, capital-of-France) to avoid confounding base-behaviour signals.
- Allow N=1 sufficiency only when the first-trial pattern is unambiguous; report N explicitly per cell.

**Does not**

- Cover the `<project_instructions>` container (Q5 skipped).
- Cover Sonnet 4.6 / Haiku 4.5 — Opus 4.7 only.
- Provide statistical power (N=2-3 per cell; sampling variance can produce edge results — Q3b is the clearest example).
- Cover mobile clients or direct API calls.
- Test combinations of containers stacked (e.g. project_instructions + CLAUDE.md + preferences in three-way conflict).

## Re-running

To replicate any cell:

1. Clear the relevant conversation history.
2. Set the Profile preferences via `Settings > Profile` (chat) or via the same Profile (Cowork inherits from chat Profile).
3. Set Global Instructions via `Settings > Cowork > Global Instructions` (saves to local CLAUDE.md). Verify save.
4. Open a fresh conversation on the relevant surface.
5. Send the query verbatim.
6. Log first 1-2 sentences of response and check the predicted marker.
7. Repeat for trial 2.
