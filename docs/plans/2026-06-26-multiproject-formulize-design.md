# Multi-Project + Formulize Persistence — Design

Date: 2026-06-26
Status: Approved (design). Implementation pending.
Scope: `site/index.html` (single-file static app) → multi-project, sidebar-managed,
persisted to Formulize via two linked forms. Export PDF + JSON per project.

---

## 1. Goals

1. **Multi-project** — manage many SeedScore assessments, not one in-memory session.
2. **Sidebar** — left panel listing all projects; switch between them; export each report.
3. **Persistence** — auto-save; back end is **Formulize** (formulize.org, PHP/MySQL,
   GPL-2.0, has an API). Build the Formulize **forms first**; wire later.
4. **Export** — per project: **PDF** (print view) + **JSON** (raw data + scores).

Non-goals (YAGNI): user auth UI, report versioning history UI, real-time multi-user sync.

---

## 2. Formulize form schema (build these first)

Decision: **two linked forms** — a `Projects` form and a linked `Reports` form.
Projects hold inputs + the 15 answers; each project links to its computed Report
entry. Sidebar lists Projects; export operates on the linked Report.

Formulize joins forms with a **linked-selectbox** element (points a field in one form
at entries of another form). Handles below match existing `site/DATA_SCHEMA-0601-.json`
and the `MODELS` array in `site/index.html`.

### Form 1 — `seedscore_project` (one entry = one project)

| Handle | Formulize element | Notes |
|---|---|---|
| `name` | text | required |
| `description` | textarea | |
| `llm_model` | select | options = MODELS names (Claude 3.5 Sonnet … Other) |
| `team_size` | text (numeric) | default 5, min 1 |
| `project_days` | text (numeric) | default 30, min 1 |
| `inference_calls` | text (numeric) | default 10000, min 0 |
| `api_cost` | text | free text, e.g. `$1,500` |
| `outcomes` | textarea | |
| `q1_core_purpose` … `q15_*` | radio (one per question) | options `0` / `1` / `2`; ids = `questions[].id` in index.html |
| `created_at` | date/text | auto on insert |
| `updated_at` | date/text | auto on update |
| owner | Formulize built-in creator uid | per-user data scoping |

15 radio elements reuse the exact question ids from `questions[]` so the app maps
`responses[id]` → element 1:1.

### Form 2 — `seedscore_report` (linked to a project)

| Handle | Formulize element | Notes |
|---|---|---|
| `project` | **linked-selectbox → `seedscore_project`** | the link; one report points at one project |
| `score_human` | text (numeric) | computed pillar score |
| `score_systems` | text (numeric) | computed pillar score |
| `score_environment` | text (numeric) | computed pillar score |
| `score_total` | text (numeric) | computed total |
| `co2_kg` | text (numeric) | model-aware CO2 estimate |
| `generated_at` | date/text | when report computed |
| `snapshot` | textarea | JSON blob of inputs+answers at generation time (reproducible export) |

Scores are **stored**, not only computed client-side, so PDF/JSON exports reproduce
without re-running JS. One report per project for now (link cardinality allows
history later without schema change).

---

## 3. Multi-project sidebar (UX)

- Left sidebar, collapsible. Lists projects: name + answered-count badge (`8/15`) +
  total-score chip (only when all 15 answered).
- Per-row actions: **open**, **export PDF**, **export JSON**, **delete**.
- Header: **+ New Project**. Active project highlighted.
- Selecting a project loads its inputs + `responses` + `currentQuestion` into the
  existing tabs (Project / Assessment / Dashboard / Report).
- Top tabs stay = in-project nav. Sidebar = cross-project nav.
- Mobile: sidebar collapses to a hamburger toggle.

---

## 4. Persistence wiring + export

Thin store interface so Formulize swaps in without touching UI:

```
Store.list()         -> [{id, name, answeredCount, scoreTotal}]
Store.load(id)       -> { project fields, responses, currentQuestion }
Store.save(project)  -> id          // upsert; writes project (+ report on generate)
Store.remove(id)     -> void
Store.saveReport(id, report)        // writes linked seedscore_report entry
```

- **Now:** localStorage-backed impl (Formulize forms not live). Auto-save debounced
  on every input/answer change.
- **Later:** Formulize-API impl against the §2 forms. `save` → Projects form entry;
  `saveReport` → Reports form entry linked via `project`. UI untouched.

Export:
- **JSON** — serialize project entry + linked report snapshot → download `.json`.
- **PDF** — print-to-PDF of the report view, scoped `@media print`, one project at a time.

---

## 5. Sequencing

1. This design doc. ← here
2. Refactor `index.html` state → `Store` interface + localStorage; multi-project model.
3. Sidebar UI.
4. Export PDF + JSON.
5. (Later) Formulize API impl behind `Store`.
