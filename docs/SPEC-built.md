---
date: 2026-09-28
version: 2.3 (through roadmap Phase 1.25)
project: Seedscore v2 (Impact Scorecard) — Built-Features Build Spec
---

# Seedscore v2 — Build Spec (built features only)

**Audience:** a developer building the app from this document.
**Scope:** the app as it exists after roadmap Phases 1.5–1.22, including the base it sits on. Planned, parked and queued features are deliberately excluded (see §15).
**Source of truth:** `index.html`. Every item below was checked against that file. Wording in quotes is verbatim.

---

## 1. Delivery format & stack

- One self-contained `index.html`. Vanilla HTML/CSS/JS. No framework, no build step, no dependencies.
- Only external resource: Google Fonts (`Space Grotesk` 500/600/700, `Lato` 400/500/700).
- UI is rendered by `innerHTML` template strings for three views (`renderQuestion()`, `renderDashboard()`, `renderReport()`); Project Setup is static markup updated by `updateComputedFields()`.
- State is in-memory JS variables only (`responses`, `currentQuestion`, `impactHorizon`, `headlineEquivalentPick`). No storage, no backend.
- Page title / `<title>`: **"Seedscore by Smol Gardens. Accountable Tech Index"**.

## 2. Design tokens

```css
--primary:#185FA5; --secondary:#0C447C;
--accent-human:#1D9E75; --accent-systems:#D85A30; --accent-env:#335C01;
--neutral:#888780;
--bg-primary:#ffffff; --bg-secondary:#F1EFE8; --bg-tertiary:#EEEDFE;
--border:#D3D1C7;
--text-primary:#2C2C2A; --text-secondary:#5F5E5A; --text-light:#B4B2A9;
--font-heading:'Space Grotesk',-apple-system,sans-serif;
--font-body:'Lato',-apple-system,sans-serif;
--chrome-black:#000000; --chrome-green:#C3FFAE;
```

- **Fonts:** Lato for body text, form labels, inputs, paragraphs. Space Grotesk for all `h1–h6`, tab buttons, primary buttons, modal titles, card sub-labels on the Dashboard, and the headline.
- **Pillar colors:** Human `#1D9E75`, Systems `#D85A30`, Environment `#335C01`.
- **Page background:** white with two fixed radial gradients (no image asset):
  `radial-gradient(circle at 73% 75%, #6cb5ff 0%, rgba(108,181,255,0) 18%)`, `radial-gradient(circle at 55% 15%, #d7ffca 0%, rgba(215,255,202,0) 55%)`; `background-repeat:no-repeat; background-attachment:fixed`.
- Layout: `.container` max-width 900px, centered. `.page-body` padding 16px (12px ≤768px). Global reset (margin/padding 0, border-box). Body line-height 1.6.

## 3. Header

- Full-width black bar (`--chrome-black`), padding 20px 16px, content centered.
- Contains one inline SVG logo (asset: `header-bar.svg`, viewBox `0 0 512 130`, fill `#C3FFAE`), **height 80px**, width auto, centered. The SVG has `role="img"` and `<title>` "Seedscore by Smol Gardens. Accountable Tech Index".
- A visually hidden `<h1 class="sr-only">` with the same text. There is no visible subtitle.
- Tabs sit **below** the header, inside `.container`.

## 4. Tabs

Four tabs in order: **Project Setup · Assessment · Dashboard · Report**. Flex row, gap 8px, 1px bottom border, `overflow-x:auto` on ≤768px.
- Button: padding 12px 20px, transparent, font Space Grotesk 14px/500, color `--text-secondary`.
- Active: black text, background `--chrome-green`, radius `4px 4px 0 0`.
- Panels toggle by `.active`; active panel fades in (0.3s, opacity 0→1, translateY 8px→0).
- `switchTab(name)` renders Assessment/Dashboard/Report content on entry.
- Navigation buttons: Project Setup "Continue →" (→Assessment; the hidden Back button keeps layout); Dashboard "← Back" / "View Report →"; Report "← Back".

## 5. Project Setup tab

A single `.question-card` (max-width 600px, centered, white, 1px border, radius 8, padding 28px). Top: a full 100% progress bar and the text "Project Details". Title (h2): **"Tell us about your project"**.

### 5.1 Fields, top to bottom

| Field | Control | Default | Behavior |
|---|---|---|---|
| Project Name | text, placeholder "e.g., Customer Service Chatbot" | empty | Used in Dashboard headline and Report; falls back to "Your Project" on the Dashboard |
| Description | text, placeholder "Brief description of your AI project" | empty | Report only |

There is no "View calculation formula" link and no Calculation Formula modal (deleted in Phase 1.24; the token math walkthrough is the "How We Calculate Inference Volumes" modal, §9.3).

**Section 1 — "The Operational Baseline"** (sub-label "Inference Calls multipliers"; 2px top border)

| Field | Control | Default | Behavior |
|---|---|---|---|
| Project Type | select | Simple Chatbot | Options below. On change: `applyProjectTypeDefault()` then `updateComputedFields()` |
| Model Tier | select, with link **"View expanded list ↗"** at right of label → Model List modal | Balanced | Updates computed fields |
| Monthly User Requests | number, min 0, link **"How We Calculate Inference Volumes ↗"** at right of label → Inference Volume modal | 10000 | Editable. Hint text below (see 5.2) plus italic "e.g., Inference Volume: ~20,000 to 30,000 total calls per month." |
| Avg Inferences per Request | number, min 0.5, step 0.5 | 1.5 | Editable. Hint under it (see 5.2) |
| Guardrails / moderation checks (doubles inference calls) | checkbox | **checked** | Multiplies calls ×2 |

Project Type options (label text verbatim):
1. `chatbot` — "Simple Chatbot — single-turn Q&A, basic support"
2. `rag` — "RAG Application — search query rewriting + generation"
3. `agentic_rag` — "Advanced RAG (Agentic) — self-correction, multi-step routing"
4. `multi_agent` — "Multi-Agent Systems — planning, execution, review, consensus"

Model Tier options (there are exactly three; Open-Weight is removed everywhere):
1. `budget` — "High-Efficiency / Budget (e.g., Claude Haiku 4.5, GPT-5.6 Luna)"
2. `balanced` — "Balanced Production (e.g., Claude Sonnet, GPT-5.6 Terra, Gemini Flash)"
3. `frontier` — "Frontier Reasoning (e.g., Claude Opus, o-series, DeepSeek V4 Pro)"

**Section 2 — "The Token Math"** (sub-label "Input vs. Output Weight"): a beige box (`--bg-secondary`, radius 8, padding 12/16) with label "Computed Monthly Inference Calls", value (18px/600, `--primary`) formatted `30,000 / month`, and below it (12px) `≈ 15.0M input · 4.5M output tokens / month`.

**There is no Financial Impact section.** The "The Financial Impact" card ("Computed Est. API Cost (monthly)") is hidden (Phase 1.23): kept in the source as a commented-out block and behind the `SHOW_COST` flag, but not rendered. The Project Setup card therefore ends after "The Token Math", then the navigation buttons.

Removed from earlier versions and must NOT exist: Project Duration, Team Size, Key Outcomes, LLM model select, manual calls/cost inputs, Active Users input, Open-Weight tier.

### 5.2 Project-type defaults (`applyProjectTypeDefault`)

| Type key | Label | Article | Range | Default inferences/request | Default monthly requests |
|---|---|---|---|---|---|
| chatbot | Simple Chatbot | a | 1–2 | 1.5 | 10,000 |
| rag | RAG Application | a | 2–3 | 2.5 | 5,000 |
| agentic_rag | Advanced RAG (Agentic) | an | 4–8 | 6 | 2,000 |
| multi_agent | Multi-Agent Systems | a | 10–30 | 20 | 500 |

Changing the type overwrites both numeric inputs and both hints:
- Inferences hint: `Based on: {label} baseline (typical range {a}–{b} inferences/request). Adjust if your deployment differs.` (initial text before first call: "Based on: Project Type baseline. Adjust if your deployment differs.")
- Requests hint: `Based on: {label} baseline. Data is illustrative — smaller-scale, more complex deployments as you go up in project complexity. Adjust if your deployment differs.`
Both are 12px `--text-secondary`. `applyProjectTypeDefault()` and `updateComputedFields()` run on page load.

## 6. Calculation engine

```
users            = Monthly User Requests (0 if empty)
monthlyCalls     = round(users × inferencesPerRequest × (moderation ? 2 : 1))
inputTokens      = monthlyCalls × 500
outputTokens     = monthlyCalls × 150
BASELINE_MONTHLY_CALLS = 30000   (10,000 requests × 1.5 × 2)
BASELINE_YEARLY_CALLS  = 3650000 (10,000 calls/day × 365)
scaleMonthly = monthlyCalls / 30000
scaleYearly  = (monthlyCalls × 12) / 3650000     // yearly is NOT monthly×12
```

Cost (Budget/Balanced only), computed as a low–high range:
`cost = (inputTokens/1e6)×inputRate + (outputTokens/1e6)×outputRate`, using the low rates for the low bound, the high rates for the high bound; displayed `$low–high` rounded to whole dollars. Frontier has no rates and shows its note text.

`fmtTokens`: ≥1e6 → `x.xM`; ≥1e3 → `x.xK`; else integer.

### 6.1 TIER_DATA (reference values, at the 30,000-call baseline)

| Tier | Label | Sublabel | Input $/1M | Output $/1M | CO₂e/mo (kg) | Water/mo (L) | Energy/mo (kWh) |
|---|---|---|---|---|---|---|---|
| budget | High-Efficiency / Budget | Haiku 4.5 / GPT-5.6 Luna | 0.20–1.00 | 1.20–5.00 | 0.007 | 3.6–7.3 | 0.03–0.09 |
| balanced | Balanced Production | Sonnet / GPT-5.6 Terra / Gemini Flash | 2.00–3.00 | 10.00–15.00 | 0.055 | 22–44 | 0.37–0.9 |
| frontier | Frontier Reasoning | Opus / o-series / DeepSeek V4 Pro | none | none | 0.29 | 110–183 | 1.8–4.4 |

Yearly reference (hardcoded, scaled by `scaleYearly`; still computed by `calculateTierMetrics` but no longer displayed anywhere after the Real-World Context card moved to the eco dataset — keep or drop at the developer's discretion):

| Tier | CO₂e/yr (kg) | Water/yr (L) | Energy/yr (kWh) |
|---|---|---|---|
| budget | 7.3 | 3,650–7,300 | 36.5–109.5 |
| balanced | 54.75 | 21,900–43,800 | 365–912.5 |
| frontier | 292.0 | 109,500–182,500 | 1,825–4,380 |

**Cost is hidden in the UI (Phase 1.23).** The cost calculation, `fmtCost`, rate data and the `SHOW_COST = false` flag stay in the code so cost can be restored; nothing on screen, in the Report, or in the JSON export shows cost. The rest of this section documents the retained logic.

Frontier cost note (verbatim, would be shown wherever a cost is displayed if restored): **"Unknown estimate, variable based on usage credit limit pricing"**.
Each monthly figure is `baseline × scaleMonthly`.

### 6.2 ECO_FOOTPRINT_DATA (independent second estimate, inference-based)

Scaled by `monthlyCalls / 30000`. Sourced from a Haiku / Sonnet / Opus breakdown. No CO₂ figure exists for it.

| Tier | Model | Energy/mo (kWh) | Water/mo (L) |
|---|---|---|---|
| budget | Haiku | 5–10 | 25–50 |
| balanced | Sonnet | 15–35 | 70–180 |
| frontier | Opus | 80–200 | 400–1000 |

Frontier note (shown under the Eco table): "Floor only — internal reasoning ("thinking tokens") can silently extend true output 2–5×, pushing actual footprint higher".

This dataset drives the **Dashboard headline**, the **Inference-Based Ecological Footprint table**, the **Real-World Context card** and the **Report's Eco Footprint Summary**. `TIER_DATA` drives the Tier Model Comparison, the Project Setup cost display and the CO₂ figures (Pillar Summary, Report). The two datasets give different water numbers in the Tier Model Comparison vs. the eco table by design.

### 6.3 Water equivalent text (`getWaterEquivalentText(waterRange)`)

Input: `[low, high]` liters from the eco estimate. The decision uses the **high end**, converted to US gallons (`gallons = high ÷ 3.78541`). The function returns a phrase that follows "equivalent to" (or "or equivalent to" in the yearly sentence).

| Gallons (high end) | Result |
|---|---|
| < 3,000 | Glasses or bottles, random per page load (`Math.random() < 0.5 ? 'glasses' : 'bottles'`): `"{round(low×4)}–{round(high×4)} glasses of drinking water"` or `"{round(low×2)}–{round(high×2)} 500ml water bottles"` (numbers with thousands separators) |
| 3,000 – < 5,000 | "about the size of a small pool, typically 10 ft x 10 ft or 12 ft x 12 ft, or a 12 ft round above-ground pool" |
| 5,000 – < 9,500 | "about the size of a small pool, typically 10 ft x 20 ft, or a 15 ft to 18 ft round above-ground pool" |
| 9,500 – < 10,000 | "about the size of a small recreational pool" (no dimensions) |
| 10,000 – < 15,000 | "about the size of a medium pool, typically 12 ft x 24 ft or 14 ft x 28 ft" |
| 15,000 – < 20,000 | "about the size of a medium pool, typically 15 ft x 30 ft or 16 ft x 32 ft, or a 24 ft round above-ground pool" |
| 20,000 – < 25,000 | "about the size of a large pool, typically 16 ft x 32 ft or 18 ft x 36 ft" |
| 25,000 – 30,000 | "about the size of a large pool, typically 20 ft x 40 ft" |
| > 30,000 | multiple = gallons ÷ 30,000 (one large residential pool = 30,000 gal). Below 1.5 → "about the size of a large residential pool". 1.5 and up → round to the nearest 0.5, drop a trailing ".0", prefix "about " when rounded: e.g. 60,000 gal → "2x large residential pool", 75,000 → "about 2.5x large residential pool" |

Each band runs up to the next band's start. Dimensions come from the pool-size reference (roadmap Reference #5). The former 68,000 L "average backyard pool" constant and the "% of a pool" wording are gone.

Range formatting `fmtRange(range, decimals, unit, note)` → `"{low.toFixed(d)}–{high.toFixed(d)} {unit}"`; `fmtVal` → `"{v.toFixed(d)} {unit}"`; a note, when present, replaces the number.

## 7. Assessment tab

15 questions, one per screen, in a question card (same card as §5). Header: progress bar (`(n/15)×100%`, fill `--primary`) and "Question n of 15". Below: uppercase 12px category label ("Human Impact" / "Systems Impact" / "Environment Impact") over the question as an h2 (18px/600). Three radio options (`survey-option` cards, 2px border): bold label **Not Met / Partially Met / Met** (values 0/1/2) with the answer text in 13px `--text-secondary`.
- Buttons: "← Back", "Next →" (becomes "Finish →" on Q15). Next with no answer shows `alert('Please answer the question before continuing.')`. After Q15 goes to Dashboard.
- Placeholder answers are preloaded so Dashboard/Report work without taking the assessment: q1=2, q2=1, q3=2, q4=1, q5=2, q6=1, q7=2, q8=1, q9=2, q10=1, q11=2, q12=2, q13=1, q14=1, q15=2.

Questions and answer text are the 15 in `assessment_questions.txt` (Human Q1–Q5, Systems Q6–Q10, Environment Q11–Q15). Copy them verbatim from that file. The array is `questions[]` with `id`, `category`, `title`, `context` (context is stored but not displayed), and three `options`.

Scoring: pillar score = average of its answered questions (0–2). Overall = mean of the three pillars. Interpretation text (Report): overall ≥1.5 → "This project demonstrates strong impact across human, systems, and environmental dimensions. The integration of AI is thoughtful, sustainable, and beneficial."; ≥1 → "This project shows balanced impact with some areas of strength and opportunity for improvement. Consider addressing the lower-scoring dimensions."; else → "This project would benefit from closer alignment with impact principles. Review opportunities to enhance human agency, system resilience, and environmental efficiency."

## 8. Dashboard tab

Order, top to bottom:

### 8.1 "ENVIRONMENT REPORT" row
Uppercase 13px label left; **"Related Research →"** button right (black bg, `#C3FFAE` text, 12px Space Grotesk 500, padding 8/16, radius 6) → opens the Related Research modal (§9.4).

### 8.2 Headline
Space Grotesk 22px/600, line-height 1.5, `--text-primary` (17px ≤768px), margin-bottom 1.5rem. Template (accent-colored parts `--accent-env`):

> Your project **{projectName}** uses **[tier ▾]** tier model, at **{monthlyCalls} monthly inference calls** requires approximately **{eco water range} liters** in data center cooling. That's equivalent to **{water equivalent}**.

- The tier is an inline `<select>` (transparent, dotted 2px underline in `--accent-env`, custom ▾ caret). Changing it sets `#modelTier`, runs `updateComputedFields()` and re-renders. Option labels are the TIER_DATA labels.
- Water range is `ECO_FOOTPRINT_DATA` scaled, `fmtRange(…,1,'liters')`. For Frontier the eco note text replaces the number only if `envNote` exists (currently none).
- The "That's equivalent to {equivalent}." sentence uses `getWaterEquivalentText` (§6.3) and is omitted if there is no result.

### 8.3 Three top cards (`.responsive-grid-3`, 3 columns; 1 column ≤768px; gap 16)
Cards: white, 0.5px border, radius 8, padding 1.5rem, centered.
1. **Inference Calls** — label (12px); number `{monthlyCalls}` Space Grotesk **24px**/700 `--accent-env`; below it **"monthly calls"** 12px/500 Space Grotesk `--accent-env`; formula line 12px: `{users} requests × {inferences}{ × 2 if moderation} inferences for {a|an} {Project Type label} project`; then a top-bordered line 11px: `≈ {input} input · {output} output tokens / month`.
2. **Token Math** — value `{input} input · {output} output` Space Grotesk **28px**/600, caption "tokens per month".
3. **Model Tier** — value = tier label, **28px**/600, caption = tier sublabel (11px).

### 8.4 Impact in Real-World Context (sits directly under the three top cards, above Tier Model Comparison)
White card. Title (14px/600) and a segmented toggle **Monthly | Yearly Projection** (default Monthly; switching re-renders via `impactHorizon`). Two beige callouts per view, each with a 4px `--accent-env` left border; callout labels are Space Grotesk 12px/500. Data comes from `calculateEcoFootprint()` (`ECO_FOOTPRINT_DATA`, §6.2) — the same token-based estimate as the headline and Report. **Yearly = the monthly estimate × 12** (no hardcoded yearly table here). There is **no Carbon Footprint callout** (the eco dataset has no CO₂ source).
- **Water Usage** — Monthly: "Your {calls} monthly inference calls used approximately **{range} liters** for data center cooling." (1 dp) Yearly: "At this pace, your projected {calls×12} annual inference calls would use approximately **{N} gallons** for data center cooling, or equivalent to {equivalent}." — {N} is the high end of the yearly water range (monthly eco estimate × 12) converted to US gallons (÷ 3.78541), 1 dp (defaults: 158.5 for Budget; 570.6 for Balanced). {equivalent} comes from `getWaterEquivalentText` (§6.3) on the yearly range, so small volumes read as bottles/glasses and large ones as a pool description; the clause is dropped if there is no result.
- **Energy Equivalent** — Monthly: "Your inference calls consumed approximately **{range} kWh** this month." (2 dp) Yearly: "Projected annual energy use of approximately **{range×12} kWh**." (1 dp)
Footer (11px): "**Source:** EESI: Data Centers and Water Consumption · Estimates from the Token Math + LLM model calculation", with the source linked to `https://www.eesi.org/articles/view/data-centers-and-water-consumption` (new tab).

### 8.5 Tier Model Comparison
White card. Title "Tier Model Comparison" (14px/600). Table, min-width 480 (horizontal scroll inside the card). Columns: Budget, Balanced, Frontier (label headers). Selected tier's header: white text on `--accent-env` with a small "SELECTED" line; selected column cells: `--bg-secondary` background, weight 600. Rows: **CO₂e / month** (3 dp, kg), **Water / month** (1 dp, L), **Energy / month** (2 dp, kWh), all from `calculateTierMetrics` at the current call volume. Footer note (11px): "Figures scale with your {calls} monthly inference calls, based on tier reference data."

### 8.6 Inference-Based Ecological Footprint
Same table pattern, headers are the model names (Haiku / Sonnet / Opus), rows **Water / month** and **Energy / month** from ECO_FOOTPRINT_DATA. Footer (11px): "A second, independently-sourced estimate — not derived from the Tier Model Comparison above. Based on a Haiku/Sonnet/Opus structural breakdown at the same monthly call volume. No CO₂ figure exists in this source, so only Water and Energy are shown." plus the Frontier floor note on a new line.

### 8.7 Seedscore Pillar Principles section (2px top border, padding-top 3rem)
- Label "Seedscore Pillar Principles" (13px/600, `--text-secondary`, normal case — no uppercase transform).
- **Impact Pillar Summary card** (white card): title "Three-Pillar Scores" (Space Grotesk 14px/600); 3-column grid of pillar score (28px/700, pillar color, 1 dp) over label (Human Impact / Systems Impact / Environment); then a top-bordered "Key Findings" block with three 13px lines:
  - "**Human Impact:** Project scored {x}/2.0. Focus areas: core purpose alignment, skill uplift, cognitive sovereignty."
  - "**Systems Impact:** Project scored {x}/2.0. Key considerations: speed/cost tradeoff, quality assurance, vendor resilience."
  - "**Environmental Impact:** Project scored {x}/2.0. Estimated CO₂ footprint: {kg} per month."
- **Three accordions** (Human, Systems, Environment), collapsed by default, each a white card. Header button: 4px×24px pillar-color bar, pillar label (Space Grotesk 16px/600), score `{x} / 2.0` in pillar color, and a ▼ chevron (rotated −90° when closed, 0.3s transition). Body: one row per question, `Q{n}: {question title}` (n prefix bold in pillar color) and its score (1 dp) right-aligned, in a 1fr/70px grid.

## 9. Modals

Shared pattern: fixed full-viewport overlay `rgba(0,0,0,0.5)`, `z-index:1000`, `display:none` → `flex`, centered, 16px padding; clicking the overlay background closes; white panel radius 12, padding 2rem, internal scroll. Header row: title (Space Grotesk 20px/700) and a 32×32 black close button (`✕`, `#C3FFAE`, radius 6). Standard panels: `max-height:85vh`.

### 9.1 Expanded Model List (max-width 800)
Title "Expanded Model List by Tier". Rebuilt on open. One section per tier (Budget, Balanced, Frontier): tier label plus a black "SELECTED" pill on the currently chosen tier, then a table (Company | Model Name | Notes; horizontal scroll, min-width 480). Data:

- Budget: Google — Gemma (e.g., Gemma 12B / Gemma 4) — Open-weight Small Language Model (SLM) for edge/local use · Meta — Llama (e.g., Llama 4 Scout) — Lightweight open-weight model with high adoption · OpenAI — GPT Luna / GPT-5-Nano / gpt-oss-20b — High-volume, low-cost structured API or local developer option · Alibaba (Qwen) — Qwen (e.g., Qwen 3.5 35B / smaller variants) — Popular open-weight SLM category · Google — Gemini Flash (e.g., Gemini 3.5 / 3.8 Flash) — Cost-floor leader with massive context and speed · Anthropic — Claude Haiku — Fast, lightweight tier for everyday text tasks · Meta — Llama (e.g., Llama 4 Maverick) — Highly efficient mid-to-budget tier open model
- Balanced: OpenAI — GPT Sol / GPT-5.5 — Versatile all-rounder for multi-step tasks and coding · Anthropic — Claude Sonnet (e.g., Sonnet 4.6 / 5) — Industry benchmark ceiling for value and reliable agent work · Alibaba (Qwen) — Qwen Max (e.g., Qwen3.7 Max) — Strong open-access/API contender for multilingual and coding workflows · Moonshot AI — Kimi (e.g., Kimi K2.5 / K3) — Popular open-contender specializing in long-context processing · Google — Gemini Pro (e.g., Gemini 3.1 Pro) — Enterprise-grade balanced model with ultra-long context windows
- Frontier: Anthropic — Claude Opus (e.g., Opus 4.7 / 5.5 / Fable) — Top-tier performance for repository-level coding and complex analysis · OpenAI — GPT-6 Astra / GPT-5.6 Sol — Flagship proprietary model for deep reasoning and agentic workflows

### 9.2 (removed)
The Calculation Formula modal and its "View calculation formula ↗" link were deleted in Phase 1.24. Its content is covered by the Inference Volume walkthrough (§9.3). The section number is kept so other references stay valid.

### 9.3 How We Calculate Inference Volumes (max-width 650)
Intro: "Here is a quick breakdown:". Sections: "Inference Volume Multipliers" — "You start with 10,000 monthly user requests." / "The Avg Inferences per Request is set to 1.5 (falling right in the 1–2 range for a simple chatbot)." / `10,000 × 1.5 = 15,000` base calls. / "Because the Guardrails / moderation checks checkbox is ticked (doubling the calls), it scales to `15,000 × 2 = 30,000 total inference calls/month`. This perfectly matches the target outcome from the chat example." — "Token Math" — "At 30,000 calls with an average of ~500 input and ~150 output tokens per turn, it correctly scales to ~15.0M input tokens and ~4.5M output tokens per month." (The "Financial Impact" section is hidden — Phase 1.23 — and kept commented out in the source.) Footer: "This walkthrough reflects the app's default state (Simple Chatbot, Balanced tier, moderation on) — change any of those and your live numbers on the form will differ from this example."

### 9.4 Related Research (full-screen)
Opened by "Related Research →". Overlay id `researchOverlay`; closes on ✕ (`aria-label="Close"`) or overlay click. Panel: max-width 900, height `calc(100vh − 32px)`, internal scroll, padding 2rem. Header: title **"Data Centers and Water Consumption: A summarized report"** (Space Grotesk 20px/700), subtitle "Seedscore: Accountable Tech Index" (13px), close button.

Body is a summary of the EESI article "Data Centers and Water Consumption" (Miguel Yañez-Barnuevo). Section headings are Space Grotesk 16px/600 with a 4px `--accent-env` bar; bullets are 13px, line-height 1.8, each starting with a bold label ("Label: text"). In order:
1. Four numbered sections: "1. The Scale of Data Center Water Consumption", "2. The Comprehensive "Water Footprint"", "3. Efficiency Metrics and Regional Impacts", "4. Sustainable Mitigation Strategies", then the paragraph beginning "**Note:** With U.S. data center electricity consumption…".
2. "The Long-Term Problem: Why Data Center Water Use is a Growing Crisis" — intro paragraph, five bullets, then "**The Bottom Line:** …".
3. **"Policy & Regulatory Responses"** — intro paragraph ("Lawmakers at both the federal and state levels…") then four sub-headings (14px/600): "1. Transparency and Mandatory Reporting Acts", "2. Stricter Water Permitting and Usage Restrictions", "3. Financial and Ratepayer Protection", "4. Eliminating Blanket Exemptions", each with its lead-in sentence and bullets.
4. **Source footer** (top border, 12px): Author: Miguel Yañez-Barnuevo · Title: Data Centers and Water Consumption · Link: "EESI Article" → `https://www.eesi.org/articles/view/data-centers-and-water-consumption` (new tab).

The exact text is in the `#researchOverlay` block of `index.html`; the verbatim source paste is Reference #4 in `roadmap.md`. The earlier Nature Scientific Reports study content and the standalone research page are retired from the app.

## 10. Report tab

A white `.report` card (padding 32, radius 8, line-height 1.8). `h2`: 20px, Space Grotesk 600, color `--primary`, margins 24/12. Paragraphs 14px `--text-secondary`. Sections in order:

1. **h2 "Impact Assessment Report"** — lines: Project (name), Description, Project Type (label), Model Tier (`{label} ({sublabel})`), Generated (`new Date().toLocaleDateString()`).
2. **h2 "Executive Summary"** — the interpretation text (§7).
3. **h2 "Seedscore Card"**
   - **h3 "Three Pillar Principles"** (16px) above a beige card (`.report-metrics`: flex, space-around, gap 20, `--bg-secondary`, padding 20, radius 8, margin-bottom 24) with three centered figures: score 28px/700 in pillar color over label 12px (Human Impact / Systems Impact / Environment).
   - **h3 "Remarks"** then three paragraphs: "**Human Impact:** Project scored {x}/2.0. Focus areas: core purpose alignment, skill uplift, cognitive sovereignty." / "**Systems Impact:** Project scored {x}/2.0. Key considerations: speed/cost tradeoff, quality assurance, vendor resilience." / "**Environmental Impact:** Project scored {x}/2.0. Estimated CO₂ footprint: {kg} per month."
4. **h2 "Environmental Impact Report"**
   - **h3 "Operational Metrics"** (outside the beige card): bullets (the "Estimated Cost" bullet is hidden — Phase 1.23 — behind `SHOW_COST`) — Monthly Inference Calls: {n}; Estimated CO₂: {kg}.
   - **Beige card** (same `.report-metrics` look, `display:block; text-align:left`) containing, in order:
     1. Heading "Eco Footprint Summary" (Space Grotesk 16px/600)
     2. Dynamic paragraph: "Your project {projectName} uses {tier label} tier model, at {calls} monthly inference calls requires approximately {eco water range} liters in data center cooling. That's equivalent to {water equivalent}."
     3. "At this scale, the footprint is modest — comparable to a short shower or a single water cooler jug. At enterprise scale, however, the picture changes substantially: scaling to 30 million monthly calls (roughly 1,000× this baseline) pushes water consumption to 70,000–180,000 liters, and industry estimates put cumulative AI-driven data center water use in the hundreds of billions of gallons annually."
     4. "The core risk isn't global water scarcity — it's localized strain on specific watersheds and municipal systems in the regions where data centers cluster (Northern Virginia, parts of Texas, Arizona, and rural Oregon among them), where large facilities can compete directly with communities and agriculture during drought."
     5. "A separate multiplier compounds this risk: autonomous agentic workflows that ping an LLM 10 to 50 times per task — self-correction, code checks, background reflection steps — can multiply an application's resource footprint tenfold, even when its visible user traffic looks unchanged."
     6. "In response, organizations are treating sustainability as an engineering requirement rather than a PR talking point:" followed by three bullets — "**Model Right-Sizing:** using smaller, optimized models (SLMs or task-specific models) for routine logic instead of defaulting to heavy frontier reasoning models for every task." / "**Caching & Retrieval Optimization:** semantic caching so repetitive queries don't trigger fresh LLM generations." / "**Infrastructure Efficiency:** closed-loop liquid cooling, zero-water cooling architectures, and co-locating data centers near renewable or nuclear energy sources to lower Water Usage Effectiveness (WUE)."
5. **h2 "Recommendations"** — conditional bullets: human avg <1.5 → "Strengthen human agency: Review decision-making processes to ensure AI augments rather than replaces judgment."; systems avg <1.5 → "Improve system resilience: Reduce vendor lock-in and document failure modes."; environment avg <1.5 → "Optimize environmental footprint: Consider smaller models, batch processing, or offset programs."; always: "Document this assessment and share findings with stakeholders for transparency." and "Plan quarterly reassessments to track progress on recommendations."
6. **h2 "How to Share & Replicate"** — "This assessment is designed to be reproducible and comparable. Share this report with others to:" + bullets: Benchmark against similar projects · Identify best practices in your industry · Build a collective understanding of AI impact · Inform organizational policy and governance.
7. **Export section** (top border): "📥 Export as JSON" and "🖨️ Print Report".

Export JSON (`impact-assessment-{timestamp}.json`): `project{name, description, projectType, projectTypeKey, modelTier, modelTierKey, monthlyUsers, inferencesPerRequest, includesModerationDoubling, monthlyInferenceCalls, estimatedMonthlyCO2kg, estimatedMonthlyWaterLiters, estimatedMonthlyEnergyKwh}`, `scores{human, systems, environment}` (2 dp strings), `responses`, `generatedAt` (ISO). `estimatedMonthlyCost` is hidden (Phase 1.23) — omitted from the export while `SHOW_COST` is false. Print calls `window.print()`; print CSS hides tabs, nav, buttons and export section and drops the report border.

## 11. Responsive rules

- ≤768px: `.responsive-grid-3` → 1 column; `.question-card` padding 20; `.dashboard-headline` font 17px; `.tabs` scrolls horizontally, no wrap; `.page-body` padding 12.
- Tables sit in overflow-x containers (min-width 480).
- Research modal result grids collapse to one column ≤600px.
- Modals use 16px overlay padding so they fit phones.

## 12. Base controls

Inputs/selects: width 100%, padding 10/12, 1px `--border`, radius 6, 14px, white; focus: border `--primary` + 3px `rgba(24,95,165,0.1)` ring. Labels: block, 14px/500, margin-bottom 8. `.btn`: padding 10/24, 1px border, radius 6, 14px/500; `.btn-primary`: black bg, `#C3FFAE` text, Space Grotesk. `.btn-export`: `--accent-human` green, white text.

## 13. Accessibility notes (as built)

Header logo has an accessible name and a hidden h1; form inputs use `<label for>`; the research modal close button has `aria-label="Close"`. Other modals/close buttons have no ARIA roles or focus trapping (not built).

## 14. Known behaviors a developer should reproduce (not bugs to "fix")

- The Tier Model Comparison table (TIER_DATA) and the Inference-Based Ecological Footprint table (ECO_FOOTPRINT_DATA) show different water/energy numbers side by side; the eco table's footer says they are independent estimates.
- The water-equivalent unit (glasses vs. bottles) is random per page load.
- Yearly figures are scaled from the hardcoded yearly table, not monthly × 12.

## 15. Explicitly out of scope (not built)

Phase 1.6 pillar-color revisit · Phase 1.10 water systemic-context research entry and conditional Report recommendation (overlap with the new Related Research content deferred to next planning) · Phase 1.13 item 3 (fate of an Active Users count) · Phase 1.18 (v1/v2 split of the Real-World Context card — relation to the now-built eco-based card still open) · Open-Weight tier and CO₂ for the eco estimate · all Phase 2 items (Data Extraction, Coding Assistant, Static Site project types, evaluation-loop multiplier, multi-modal multiplier, granular moderation) · any persistence, backend or accounts.

## 16. Files a developer needs

- `index.html` — reference implementation (single file; `styles.css` and `app.js` are extracted copies for reading only)
- `header-bar.svg` — logo asset (inlined in the header)
- `assessment_questions.txt` — the 15 questions and answer text, verbatim
- `roadmap.md` — decision history (context only, not needed to build)

---
