# 2026-03-09 — Session Close

## What was completed this session

1. **Folder structure + git init** — full directory tree per README.md, .gitignore, initial commit.
2. **Synthetic sample corpus** — 50 questions across 6 domains in `data/question-corpus/sample-corpus.json`.
3. **End of Session Checklist** — added to CLAUDE.md.
4. **Submission interface** (`prototypes/track-1-expert-panel/submission-interface/`) — SPEC.md + single-file CSV upload app. Template download, per-row validation, review table, JSON export.
5. **Panel tooling** (`prototypes/track-1-expert-panel/panel-tooling/`) — SPEC.md + single-file app. Setup → triage → rank → export. Domain filtering, random shuffle, auto-advance, drag-and-drop ranking.
6. **GitHub repo + Pages** — https://github.com/edwardrmiller/prescience-benchmark, live at https://edwardrmiller.github.io/prescience-benchmark/. Landing page with status for all prototypes.
7. **Bug fixes** — upload zone border (label → div), .nojekyll for Pages, rationale rendering on triage card.

## State at close

- Build order steps 1–2 (corpus + Track 1 prototypes) are complete.
- Step 3 (scoring engine, `scripts/scoring/`) is next.
- Step 4 (market signal validator) follows after.

## Next session — pick up here

**Scoring engine** (`scripts/scoring/`):
- Python CLI
- Inputs: one or more `ratings-{id}-YYYY-MM-DD.json` files from the panel tooling
- Outputs: prescience score per question (retro − prospective), participant leaderboard
- Write SPEC.md first, then implement
- Open question: aggregation function (mean vs Top-K mean) — OQ-8 still unresolved; implement both and make it a CLI flag

## Open questions

- **OQ-8**: Mean vs Top-K mean for participant score aggregation
- **OQ-9**: Whether to show supporting rationale to panellists at retrospective stage (currently shown at triage only)
- Debug sample corpus download link in panel tooling — remove before final version
