# Seedscore — MVP Plan (Week 1-2)

Scope: Fix DATA_SCHEMA, make Q1–Q15 validation work, add small models to dropdown,
align Dashboard scorecard with questionnaire + brief. Cadence: Tue 9am EST review.

App = single static file `site/index.html` (~1270 lines) + `site/DATA_SCHEMA-0601-.json`.
No build, no deps, no server. Opens directly in browser.

---

## Status — what works today

| Area | State |
|------|-------|
| 4 tabs (Project → Assessment → Dashboard → Report) + nav | ✅ works |
| 15 questions, 3 pillars (human/systems/environment), ids q1–q15 | ✅ present, content matches canonical word-for-word |
| Scoring 0/1/2, pillar = average(sum/count), shown as `x.x / 2.0` | ✅ correct (`calculateScores`, line 941) |
| Option labels Not Met / Partially Met / Met | ✅ matches canonical (line 894) |
| Dashboard: pillar scores + Environment report (CO₂, calls, model) | ✅ renders |
| Report: per-pillar summary + conditional recommendations (`avg < 1.5`) | ✅ works |
| JSON export (`exportReport`, line 1232) | ✅ works (Blob download) |
| Print report (`window.print`) | ✅ works |
| Per-question "answer before continuing" guard (line 922) | ⚠️ exists but DEFEATED — see Bug 1 |

## Status — what's broken (the week 1-2 work)

| # | Bug | Location | Impact |
|---|-----|----------|--------|
| 1 | `initializePlaceholderResponses()` pre-fills all 15 answers with fake values on page load, runs at line 824 | 803–824 | **Critical.** Validation guard (`responses[id] === undefined`) never fires → user can skip every question. Dashboard shows fake scores before any input. This is the #1 acceptance blocker: "Validation for Q1–Q15 must work once the user enters their answer." |
| 2 | Model dropdown missing small models (brief: Qwen 8B, Haiku 4.5, SmolLM) | 560–567 | Dropdown has Claude 3.5 Sonnet, Claude 3 Opus, GPT-4, GPT-4 Turbo, Llama 2, Other. |
| 3 | 3-way model-list mismatch — no single source of truth | HTML 561 vs schema enum 21 vs `emissionsData.models` 371 | HTML has Llama 2 / no Gemini; schema enum has Gemini Pro / no Llama; emissionsData lists yet another set. "Fix DATASCHEMA.json code." |
| 4 | Schema filename `DATA_SCHEMA-0601-.json` ≠ docs reference `DATA_SCHEMA.json` | filename + README/QUICK_START | Broken reference; rename file or fix doc refs. |
| 5 | App never loads the schema JSON — dropdown + emissions hardcoded inline | whole `<script>` | Schema is dead config. Must wire as source of truth (or at minimum sync the three lists). |
| 6 | CO₂ formula hardcoded `calls * 1200 * 0.000014`, ignores per-model `emissionsData` | 967 | Env calc not model-aware. (Full fix = Week 3-4, but rooted here.) |
| 7 | Docs drift: QUICK_START/README say "13 questions" in places, "Fully Met" label | docs | Brief: "Update QUICK START to be accurate to our work." Actual = 15 q, label "Met". |

---

## Plan

### Week 1 — DATA_SCHEMA fix + validation

**T1. Kill placeholder pre-fill (unblocks validation)** — Bug 1
- Remove `initializePlaceholderResponses()` body + its call at line 824. Start `responses = {}`.
- Dashboard/Report must handle empty/partial responses without crashing (pillar avg already guards `length > 0`; verify `displayScore` shows `—`).
- Acceptance: opening Assessment shows no answer pre-selected; "Next" blocks until current question answered for all Q1–Q15.

**T2. Per-question validation hardening** — Bug 1
- Keep the `undefined` guard; also block jump to Dashboard until all 15 answered (or show "X of 15 answered, finish to view scorecard").
- Inline error under question instead of `alert()` (better UX, still MVP-cheap).
- Acceptance: cannot reach a scored Dashboard with any of Q1–Q15 unanswered.

**T3. Single source of truth for models** — Bugs 2, 3, 5
- Define one `models` array in JS (name + emissions factor + size tag), used to build both the dropdown and the emissions lookup.
- Add small models: **Qwen 8B, Claude Haiku 4.5, SmolLM** (+ keep existing where still valid).
- Make `DATA_SCHEMA-0601-.json` enum + `emissionsData.models` match that same list.
- Acceptance: dropdown, schema enum, emissions table list identical model set; small models selectable.

**T4. Fix DATA_SCHEMA file** — Bug 4
- Rename `site/DATA_SCHEMA-0601-.json` → `site/DATA_SCHEMA.json`; update all doc references.
- Validate JSON parses; ensure `responses` schema = "questionId → 0|1|2" for all 15 ids.
- Acceptance: filename matches docs, JSON valid, enum synced (T3).

### Week 2 — Dashboard aligned to questionnaire + brief

**T5. Scorecard prioritization logic** — brief "Prioritize logic for Scorecard"
- Order/flag pillars by weakest avg; surface lowest-scoring pillar + its weakest questions first.
- Confirm 0 / 1.0 / 2.0 mapping and per-pillar average shown as `/2.0`. Add overall = mean of 3 pillars (docs show "Overall").
- Acceptance: scorecard reflects real answers, weakest pillar highlighted, matches 0/1/2 system.

**T6. Dashboard ↔ questionnaire alignment**
- Every pillar block lists its 5 real questions + the user's actual response (no fake data after T1).
- Recommendations keyed to actual weak questions, not hardcoded prose.
- Acceptance: dashboard content is 1:1 with the 15 answered questions.

**T7. Docs accuracy pass** — Bug 7
- QUICK_START.md + README: fix "13 → 15 questions", "Fully Met → Met", DATA_SCHEMA filename.
- Acceptance: docs describe the tool as actually built.

### Verification (every Tue review)
1. Open `site/index.html` in browser.
2. Project tab → pick a small model (Qwen 8B / Haiku 4.5 / SmolLM).
3. Assessment → confirm Next blocks on each unanswered Q1–Q15.
4. Answer all 15 → Dashboard shows real per-pillar averages /2.0, weakest pillar flagged.
5. Export JSON → `responses` has all 15 ids, scores match dashboard.

---

## Out of scope (Week 3-4 / Beyond)
- Model-aware CO₂ formula + Environmental section data fix (Week 3-4, Bug 6).
- TXT export, download-report MVP (Week 3).
- Export History sidebar / per-project export links (Beyond Scope).
