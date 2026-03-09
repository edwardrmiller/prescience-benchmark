# 2026-03-09 — Panel Tooling

## What was built

- **SPEC.md** — written before code. Defines the two-stage workflow, input/output schemas, stack, and validation steps.
- **index.html** — single-file panel tooling prototype. Four screens:
  1. Setup — load corpus JSON, enter panellist ID, select domains
  2. Triage — one question at a time, 5-point rating, auto-advance
  3. Rank — drag-and-drop + ↑↓ buttons for the 4–5 cluster
  4. Export — session summary + JSON download

## Key design decisions

- **Domain filtering at start**: questions are filtered server-side (JS) to only those whose `domain_tags` intersect the panellist's chosen domains. This matches the tournament routing rule.
- **Random shuffle on each session start**: prevents ordering bias across panellists. Seed is not recorded (sufficient for prototype; a seeded PRNG would be needed for reproducibility in production).
- **Auto-advance on rating** (280ms delay for visual feedback) is the primary flow. Back/Next buttons allow correction without leaving the page.
- **Ranking initialised score-descending**: ties broken by original corpus order. Panellist can then reorder freely.
- **Drag-and-drop** uses native HTML5 `draggable` API. Drop target detection uses midpoint heuristic (above/below centre of target element). ↑↓ buttons provide an accessible, keyboard-friendly fallback.
- **Ranking skipped if < 2 questions rated 4–5**: goes directly to export with the single top-rated question (if any) included in `ranking`.
- **Output schema** produces `triage` (all ratings) and `ranking` (4–5 cluster only, ordered). This is the input format the scoring engine will consume.

## Open questions

- OQ-9: Supporting rationale visibility. Currently not shown to panellists at all. When resolved, add a toggle to the triage card.
- Should the panellist be able to skip a question and return to it later, rather than being forced to rate before advancing? May reduce fatigue on long queues.
- Production version will need session persistence (resume after browser close) and a seeded shuffle for reproducibility across replays.

## Next steps

1. Scoring engine — `scripts/scoring/` — Python CLI. Ingests triage ratings + ranking from one or more panellists, outputs prescience scores per question and per participant.
