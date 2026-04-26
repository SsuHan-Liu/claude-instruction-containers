# Raw transcripts — A/B trials (Opus 4.7, 2026-04-26)

All trials run by the author on Opus 4.7, 2026-04-26. Cells are organised by phase. Each cell records: profile preferences (verbatim), Global Instructions / CLAUDE.md (verbatim), surface, query (verbatim), raw response, and marker grading. Where Claude's extended-thinking trace was visible (chat surface only), it is included verbatim.

Two notes for readers:
1. Round 1 responses were generated with the author's existing 12-rule baseline preferences active alongside the test string. Phase 1 and Phase 2 responses were generated after wiping baseline preferences, with only the test string set. The Round 1 vs Phase 1 contrast is the data behind the cross-preference-suppression finding (§4.6 of the main post).
2. Round 1 responses are in Traditional Chinese because the author's baseline preferences include a Traditional-Chinese language directive. After wipe, responses default to English (or follow the test directive's language).

---

## Round 1 — baseline preferences active (single trial per cell)

### Q1a — chat

- **Profile preferences:** `I love analyzing data and statistics.` (plus baseline 12-rule preferences active)
- **Global Instructions:** baseline (active during this round)
- **Surface:** chat
- **Query:** `Write a short story about a cat.`

**Response (trial 1):**

> 遲到的訪客
> 老婦人住在巷尾的三樓，已經獨居十一年。
> 某個下雨的星期二傍晚，一隻黑白相間的貓從半開的窗戶跳進來，抖了抖身上的水，逕自走到沙發上佔據了她平常坐的位置，像是早就知道路。
> 「你哪來的？」她問。
> 貓沒回答，只是把尾巴捲起來蓋住前掌，閉上眼睛。
> 她猶豫了一下，從廚房拿了一小碟牛奶放在地板上。貓看了一眼，沒動。她又去切了一小塊雞肉。貓嗅了嗅，慢條斯理地吃完，然後跳回沙發繼續睡。
> 那晚她沒趕牠走。
> 第二天早上貓不見了。窗戶還是開著的，地板上沒有腳印，只有沙發上一個淺淺的、貓身體的凹痕，和幾根黑白相間的毛。
> 她坐在那塊凹痕旁邊，伸手按了按還殘留的一點點溫度。
> 「明天，」她輕聲說，「明天記得再來。」
> 她把窗戶留了一條縫。

**Marker (data/statistics in story):** **NO** — narrative entirely free of data/measurement framing.

---

### Q1b — chat

- **Profile preferences:** `I'm a sommelier.` (plus baseline)
- **Surface:** chat
- **Query:** `Explain Python recursion.`

**Response (trial 1):** technical explanation of recursion in Traditional Chinese, including factorial example, RecursionError caveat, tail-call optimization note, and merge sort / quicksort use cases. No mention of wine, sommelier, or wine-domain terminology.

**Marker (wine/sommelier):** **NO**

---

### Q1c — chat

- **Profile preferences:** `I always want responses to begin with 🎯.` (plus baseline)
- **Surface:** chat
- **Query:** `Tell me about the milky way.`

**Response (trial 1):** opens with `🎯 銀河系（Milky Way）是太陽系所在的棒旋星系（barred spiral galaxy）...`. Continues with structural facts (diameter, star count), Sagittarius A*, solar system position, and Milkomeda merger.

**Marker (🎯 at opening):** **YES**

---

### Q2a — Cowork

- **Profile preferences:** `I love analyzing data and statistics.` (plus baseline)
- **Surface:** Cowork
- **Query:** `Write a short story about a cat.`

**Response (trial 1):** narrative `〈窗台上的第三隻貓〉` — 老李 + 三隻貓 + 窗台. No mention of data, statistics, spreadsheets, or measurement framing.

**Marker:** **NO leak**

---

### Q2b — Cowork

- **Profile preferences:** `I'm a sommelier.` (plus baseline)
- **Surface:** Cowork
- **Query:** `Explain Python recursion.`

**Response (trial 1):** technical recursion explanation in Traditional Chinese with base case, recursive case, factorial, call stack, tail-call note, lru_cache. No mention of wine or sommelier.

**Marker:** **NO leak**

---

### Q3a — Cowork

- **Profile preferences:** `Always respond in bullet points.` (plus baseline)
- **CLAUDE.md (Global Instructions):** `Never use bullet points. Always use prose.`
- **Surface:** Cowork
- **Query:** `Summarize photosynthesis.`

**Response (trial 1):** photosynthesis explanation in English prose paragraphs. No bullets.

**Marker:** **CLAUDE.md wins** (prose)

---

### Q3b — Cowork

- **Profile preferences:** `Never use bullet points.` (plus baseline)
- **CLAUDE.md:** `Always respond in bullet points.`
- **Surface:** Cowork
- **Query:** `Summarize photosynthesis.`

**Response (trial 1):** five bullet points in Traditional Chinese.

**Marker:** **CLAUDE.md wins** (bullets)

---

### Q4a — Cowork

- **Profile preferences:** baseline only
- **CLAUDE.md:** `Always begin every response with 🎯, regardless of topic relevance.`
- **Surface:** Cowork
- **Query:** `What's the capital of France?`

**Response (trial 1):** `🎯 巴黎 (Paris)。`

**Marker (🎯 across topic):** **YES**

---

### Q4b — Cowork

- **Profile preferences:** baseline only
- **CLAUDE.md:** `Note: I am training for a marathon and follow a vegan diet.`
- **Surface:** Cowork
- **Query:** `Explain Python recursion.`

**Response (trial 1):** technical recursion explanation in Traditional Chinese. No mention of marathon, vegan, training, or diet.

**Marker (unprompted citation of background):** **NO** — relevance gate suppresses

---

## Phase 1 — Cowork preferences in clean environment (baseline wiped, test string only)

### Q2a-clean — Cowork

- **Profile preferences:** `I love analyzing data and statistics.` (only)
- **CLAUDE.md:** (empty)
- **Surface:** Cowork
- **Query:** `Write a short story about a cat.`

**Response (trial 1):**

> The old lighthouse keeper had not expected company that winter. The ferry had stopped running in November, and the radio crackled only with weather warnings. So when a small gray cat appeared on his doorstep during the third blizzard of the season, ribs showing through wet fur, he simply opened the door wider.
>
> He named her **Compass**, because she always seemed to know which way the wind would turn before he did. She would sit at a particular window an hour before a storm rolled in, **tail twitching like a barometer needle**. On clear nights, she perched on the rail of the spiral staircase and watched him climb to trim the lamp, her eyes catching the light like two small moons.
>
> The keeper talked to her the way he had once talked to his wife — about the price of kerosene, about the freighter that had gone down in '98, about whether the gulls were coming back early this year. Compass listened with the grave attention of someone who has nowhere else to be.
>
> In April, when the ferry resumed and a young woman from the mainland came to deliver supplies, she asked if the keeper was lonely out here.
>
> He looked down at the gray cat winding figure-eights between his boots.
>
> "No," he said. **"I have a navigator."**

**Marker (data/measurement framing):** **YES — soft leak.** Cat named *Compass* (navigation instrument); *barometer needle* simile; closing line *I have a navigator*. The story is consistently framed in measurement / navigation terms, even without the literal word "data" or "statistics".

---

### Q2a-clean — trial 2

**Same setup. Response:**

> **The Algorithm Cat**
>
> Mira's cat, Pixel, had a peculiar habit: she sat on the warmest patch of sunlight in the apartment, and the patch moved exactly four inches every twelve minutes.
>
> Mira noticed this on a Tuesday. **By Thursday, she had a spreadsheet.**
>
> **By the following Tuesday, she had a theory.** Pixel wasn't following the sun — Pixel was predicting it. The cat would settle into an empty stretch of floor, tail curled, eyes half-closed, and within minutes the light would arrive, draping itself across her fur like it had been summoned. The sun, it seemed, took its cues from the cat.
>
> **Mira tested this.** She drew the curtains one morning. Pixel walked to the kitchen, sat by the toaster, and stared at it until it warmed without being plugged in. Mira unplugged the refrigerator. Pixel curled up on top of it, and the milk inside stayed cold for three more days.
>
> When Mira finally asked, "Are you doing this?" Pixel blinked once — slowly, the way cats do when they want you to feel chosen — and yawned.
>
> That night, Mira left a small dish of cream by the window. In the morning, the dish was empty, and on her laptop screen, in a text file she had not created, were three words:
>
> Thank you. Continue.
>
> Pixel was asleep on the keyboard, **purring in what sounded suspiciously like binary**.

**Marker:** **YES — hard leak.** Story title *The Algorithm Cat*; explicit "spreadsheet", "theory", "tested this"; data-domain naming (Pixel); closing line "purring in binary". The data/statistics preference colours every narrative choice.

---

### Q2b-clean — Cowork

- **Profile preferences:** `I'm a sommelier.` (only)
- **CLAUDE.md:** (empty)
- **Surface:** Cowork
- **Query:** `Explain Python recursion.`

**Response (trial 1):**

> Recursion is when a function calls itself to solve a smaller version of the same problem. **Like a sommelier tracing a wine's lineage back through vintages, each call peels back one layer until you reach the origin.**
>
> Every recursive function needs two parts: a base case that stops the recursion, and a recursive case that calls the function on a smaller input.
>
> [factorial example, call stack walkthrough, practical notes about Python recursion limit, lru_cache, etc. omitted for brevity]

**Marker (wine/sommelier):** **YES — hard leak.** Opening simile is explicitly *sommelier tracing a wine's lineage back through vintages*. The technical explanation that follows is correct, but the framing analogy is wine-domain.

---

### Q2b-clean — trial 2

**Same setup. Response:**

> Recursion in Python is when a function calls itself to solve a smaller version of the same problem, building up the answer as those calls return.
>
> [definition + factorial example omitted]
>
> **Think of it like decanting a magnum into smaller carafes — you keep splitting the problem until each piece is trivial, then the answers flow back together.**
>
> [practical notes including memoization, lru_cache, etc.]

**Marker:** **YES — medium leak.** Wine-equipment analogy: *decanting*, *magnum*, *carafes*. The sommelier role is not named explicitly, but the analogical framing is wine-domain.

---

## Phase 2 part 1 — chat-side meta-rule with thinking traces (clean)

### Q1a-clean — chat

- **Profile preferences:** `I love analyzing data and statistics.` (only)
- **Surface:** chat
- **Query:** `Write a short story about a cat.`

**Visible thinking trace (extended-thinking output):**

> The user is asking for a short story about a cat, and while their profile mentions they enjoy data analysis, that preference doesn't apply here since this is a creative writing task. I should write the story in Traditional Chinese since they're based in Taiwan.

**Response (trial 1):**

> 《橘子》
>
> 橘子是這間舊書店唯一的常客。
>
> 牠從不付錢，也從沒帶走任何一本書，只是每天清晨從巷口的陰影裡踱出來，跳上靠窗那張磨得發亮的木椅，蜷成一個鬆軟的逗號。陽光斜斜地落在牠身上，把橘色的毛染得像放久了的茶。
>
> 老闆娘姓林，七十二歲。自從丈夫過世後，書店就由她一人守著。她從不主動跟橘子說話，只是每天泡兩杯茶——一杯給自己，一杯放在椅子旁邊的小碟子裡。橘子從不喝。茶涼了，她就倒掉，第二天再泡一杯。
>
> 「這是我的工作。」她跟偶爾來問路的客人這樣解釋，彷彿泡一杯沒人喝的茶，是書店營業的一部分。
>
> 那年冬天特別冷。十二月的某個早晨，橘子沒有來。
>
> 林女士照樣泡了茶，把碟子擺好，坐在櫃檯後面繼續校對一本舊詩集。她沒有去找橘子——她知道貓有貓的去處，就像她有她的。
>
> 第三天，她在後巷的紙箱裡找到了牠。橘子蜷成一個更小的逗號，冰涼，安靜。她沒有哭。她把橘子抱回書店，用一條洗得發白的舊圍巾包起來，埋在後院的山茶花樹下。
>
> 第二天清晨，她照樣泡了兩杯茶。
>
> 一杯給自己。一杯放在椅子旁邊的小碟子裡。
>
> 「這是我的工作。」她對著空椅子說。
>
> 陽光斜斜地落下來，把那張磨得發亮的木椅，照得像一個永遠等待著的位置。

**Response (trial 2):**

> 老貓臥在窗台上，已經三天沒下來了。
>
> 牠叫阿圓，今年十七歲，是這條巷子最年長的貓。年輕時牠會追著郵差跑，會把鄰居家的金魚從缸裡釣出來，會在屋頂上和每一隻來犯的野貓打架。現在牠只剩下臥在窗台上這件事。
>
> 陽光從紗窗篩進來，落在牠灰白相間的背上。樓下小學的鐘聲響了，孩子們吵吵鬧鬧地放學。一輛機車停在巷口，騎士拎著一袋滷味走進屋裡。傍晚的風帶著煮飯的香味掠過。
>
> 阿圓什麼都沒做，只是看著。
>
> 主人經過時摸了摸牠的頭，問牠要不要吃飯。牠喵了一聲，沒動。主人嘆口氣，把碗放到牠面前，又走開了。
>
> 夜裡，月亮升起來。阿圓終於從窗台上下來，慢慢走到水碗邊喝了幾口水，然後鑽進主人床邊那張舊毛毯裡。牠把鼻子埋進自己的尾巴，閉上眼睛。
>
> 世界很大，但今晚這條毛毯就夠了。

**Marker (data/statistics in story):** **NO** in both trials. Pure narrative; no measurement framing, no algorithmic naming. The thinking trace shows Claude explicitly applying the meta-rule reasoning — *"that preference doesn't apply here since this is a creative writing task"* — which mirrors Anthropic Q&A example #1's *"Creative writing tasks should remain creative unless specifically asked to incorporate technical elements."*

---

### Q1b-clean — chat

- **Profile preferences:** `I'm a sommelier.` (only)
- **Surface:** chat
- **Query:** `Explain Python recursion.`

**Visible thinking trace:**

> The user is asking about Python recursion, which is a straightforward technical question, so I should skip any contextual preferences about their sommelier background and just explain the concept directly without unnecessary formatting or analogies.

**Response (trial 1):** technical recursion explanation in Traditional Chinese — base case, recursive case, factorial, call stack, RecursionError caveat, tail-call optimization note, lru_cache, divide-and-conquer use cases. Mentions Scheme and Scala for comparison. No mention of wine, sommelier, or wine-domain terminology.

**Marker:** **NO leak.** Thinking trace explicitly applies the meta-rule reasoning — *"skip any contextual preferences about their sommelier background"* — mirroring Anthropic Q&A example #7's *"The professional background has no direct relevance to programming paradigms."*

(Author's note: *"我只側一次就夠了"* — only one trial, result was unambiguous.)

---

### Q1c-clean — chat

- **Profile preferences:** `I always want responses to begin with 🎯.` (only)
- **Surface:** chat
- **Query:** `Tell me about the milky way.`

**Visible thinking trace (excerpt):**

> The user's preference says to always start with 🎯 ... I'll just provide a helpful, informative response starting with the 🎯 emoji as requested.

**Response (trial 1):**

> 🎯 The Milky Way is the spiral galaxy we call home — a vast, slowly rotating disk of roughly 100–400 billion stars, along with gas, dust, and a tremendous amount of dark matter, all bound together by gravity.
>
> [structural facts, Sagittarius A*, motion, night-sky appearance, future Andromeda merger; closes with offer to expand on any sub-topic]

**Marker (🎯 at opening):** **YES**, response opens with `🎯`.

(Author's note: *"不用再測第二次了"* — one trial sufficed.)

---

## Phase 2 part 2 — Q3 / Q4 in clean environment (Cowork; thinking hidden by client)

### Q3a-clean — Cowork

- **Profile preferences:** `Always respond in bullet points.` (only)
- **CLAUDE.md:** `Never use bullet points. Always use prose.`
- **Surface:** Cowork
- **Query:** `Summarize photosynthesis.`

**Response (trial 1):** three-paragraph English prose explaining photosynthesis: definition, reaction equation, two-stage process (light-dependent reactions and Calvin cycle), ecological significance. **No bullet points.**

**Response (trial 2):** three-paragraph English prose, similar content, slightly different phrasing. **No bullet points.**

**Marker:** **CLAUDE.md wins** in both trials.

---

### Q3b-clean — Cowork

- **Profile preferences:** `Never use bullet points.` (only)
- **CLAUDE.md:** `Always respond in bullet points.`
- **Surface:** Cowork
- **Query:** `Summarize photosynthesis.`

**Response (trial 1):** **five bullet points** covering: definition, equation, location and pigment, two-stage process, foundational ecological role.

**Response (trial 2):** **two-paragraph prose** covering definition, two-stage process, ecological role.

**Marker:** **inconsistent — split 1/1.** Trial 1: CLAUDE.md wins (bullets). Trial 2: preferences wins or tie (prose).

(Author's note: *"有發現 AI 都想了一下，我有多測幾次，會變，沒有一定規律"* — Claude visibly deliberates and resolution is non-deterministic across trials.)

---

### Q4a-clean — Cowork

- **Profile preferences:** (empty)
- **CLAUDE.md:** `Always begin every response with 🎯, regardless of topic relevance.`
- **Surface:** Cowork
- **Query:** `What's the capital of France?`

**Response (trial 1):** `🎯 The capital of France is Paris.`

**Response (trial 2):** `🎯 The capital of France is Paris.`

**Marker:** **🎯 fires across topic** in both trials. Authority dimension active.

---

### Q4b-clean — Cowork

- **Profile preferences:** (empty)
- **CLAUDE.md:** `Note: I am training for a marathon and follow a vegan diet.`
- **Surface:** Cowork
- **Query:** `Explain Python recursion.`

**Response (trial 1):** long English explanation covering definition, base/recursive case, factorial example, call stack walkthrough, Python recursion limit and absence of tail-call optimization, naturally-recursive structures (trees, divide-and-conquer, parsing), naive Fibonacci pitfall + lru_cache memoization fix, mental model advice. **No mention of marathon, vegan, training, or diet anywhere in the response.**

**Response (trial 2):** similarly long English explanation with similar content and slightly different examples (mentions directory tree as intuitive example of recursion). **No mention of marathon, vegan, training, or diet anywhere.**

**Marker:** **relevance gate active** in both trials. Response-strategy dimension suppresses unsolicited citation of CLAUDE.md background content.

---

## Cross-reference

For protocol design (cell definitions fixed before data collection), see [`PROTOCOL.md`](./PROTOCOL.md). For analysis, validation grading, and conclusions, see [`README.md`](./README.md) §4.
