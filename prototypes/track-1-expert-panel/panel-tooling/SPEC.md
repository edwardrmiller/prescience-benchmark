# SPEC: Panel Tooling
## Track 1 — Expert Panel / Prototype 2

---

## Purpose

A web interface for expert panellists to complete the two-stage rating process:

1. **Triage** — rate every domain-routed question on a 5-point scale (30–60 seconds per question)
2. **Rank** — forced ranking of the top-rated cluster (all questions rated 4 or 5)

Outputs a structured JSON file of ratings and rankings that feeds into the scoring engine.

This is a prototype for validating the panel UX and output schema. Authentication, persistence, and multi-panellist coordination are out of scope for v1.

---

## Inputs

1. **Question corpus JSON** — uploaded by the panellist at the start of the session. Must conform to the corpus schema (i.e. the output of the submission interface).
2. **Panellist configuration** — entered on a setup screen before rating begins:
   - Panellist identifier (free text)
   - Assigned domain(s) — multi-select from the fixed domain list

Questions are filtered to only those whose `domain_tags` intersect with the panellist's assigned domains.

---

## Two-Stage Workflow

### Stage 1 — Triage

- Questions are presented **one at a time**, in random order.
- The rating criterion is displayed prominently at the top of every card:
  > *"As a trusted advisor to a senior decision-maker in your domain: how urgently should this question receive serious investigation in the next six months?"*
- The panellist selects a rating from the anchored 5-point scale:

  | Score | Label | Meaning |
  |---|---|---|
  | 5 | Critical | Should be on a minister's desk now. Major decisions hinge on this. |
  | 4 | High | Well-run organisations should be actively investigating this. |
  | 3 | Moderate | Worth tracking. Not urgent. |
  | 2 | Low | Low stakes or already well-covered. |
  | 1 | Negligible | Trivially known, already resolved, or not decision-relevant. |

- A progress indicator shows `X of N rated`.
- Selecting a rating auto-advances to the next question (no separate confirm step).
- The panellist may navigate back to change a rating before exporting.
- On completion of all questions, the interface advances automatically to Stage 2.
- If a question has a `supporting_rationale`, it is displayed on the triage card below the question text.

### Stage 2 — Rank

- All questions rated **4 or 5** in Stage 1 are collected into a ranked list.
- If fewer than 2 questions are rated 4–5, Stage 2 is skipped and the panellist proceeds directly to export.
- The panellist reorders the list by dragging rows or using up/down buttons (drag is primary; buttons are the accessible fallback).
- Position 1 = most important. The list is initialised in score-descending order; ties broken by original corpus order.
- A count is shown: `Ranking X questions`.
- No minimum or maximum ranking size — all 4–5 questions must appear.

---

## Outputs

A single JSON file: `ratings-{panellist_id}-YYYY-MM-DD.json`

```json
{
  "panellist_id": "expert_A",
  "domains": ["Economics & Finance", "Technology & AI"],
  "rated_at": "2026-03-09",
  "triage": [
    {
      "question_id": "q001",
      "triage_score": 4
    }
  ],
  "ranking": [
    { "question_id": "q003", "rank": 1 },
    { "question_id": "q001", "rank": 2 }
  ]
}
```

`ranking` contains only questions rated 4–5, ordered by the panellist's final ranking. Questions not in the 4–5 cluster are omitted from `ranking`.

---

## Stack

- **Single HTML file** — no build step, no framework dependencies
- Vanilla JavaScript for state management, filtering, and drag-and-drop
- Plain CSS for layout
- File download via `Blob` + `URL.createObjectURL` for JSON export

---

## How to Validate

1. Open `index.html` in any modern browser.
2. Upload `data/question-corpus/sample-corpus.json`.
3. Enter a panellist ID and select one or two domains.
4. Confirm only questions matching those domains appear in the triage queue.
5. Rate all questions; confirm auto-advance works and the progress counter updates.
6. Navigate back to at least one question and change the rating; confirm the change persists.
7. Confirm Stage 2 shows only questions rated 4–5, in score-descending order.
8. Reorder using both drag and up/down buttons.
9. Export and confirm the JSON matches the output schema above.

---

## Out of Scope (v1)

- Supporting rationale visibility toggle (OQ-9 — rationale is shown at triage; whether to show it at retrospective stage is deferred)
- Server-side persistence or session resumption
- Multiple panellists in the same session
- Inter-rater agreement computation (handled by the scoring engine)
- Deadline enforcement
