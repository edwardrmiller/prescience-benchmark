# 2026-03-09 — Submission Interface

## What was built

- **End of Session Checklist** added to CLAUDE.md (update Session Log → write note → git commit).
- **SPEC.md** written for the submission interface before any code — inputs, processing, outputs, stack choice, validation steps, and explicit out-of-scope list.
- **index.html** — single-file submission interface, no build step required. Open in any browser.

## Key design decisions

- **Single HTML file** chosen deliberately: stakeholders can run it without a server, Node, or Python. The tradeoff is no persistence across sessions, which is acceptable for a prototype.
- **In-memory state only**: questions live in a JS array. Export via `Blob` download. No backend needed at this stage.
- **50-question cap** enforced client-side: submit button disables, counter pill turns red.
- **Impact self-assessment is optional** (matches schema). Rating buttons toggle — click the same value again to deselect.
- **Supporting rationale** capped at 200 words (matches CLAUDE.md schema), counted live.
- **Domain tags**: max 3, enforced by unchecking a fourth selection.

## Open questions carried forward

- OQ-9: Should supporting rationale be visible to panellists at prospective stage, retrospective stage, or both? Affects what the panel tooling needs to display/hide.
- Should the submission interface enforce a deadline (submission window close date)? Currently out of scope for v1.
- Will participants submit via a shared URL (hosted) or run locally? Affects whether we need a backend before moving out of prototype phase.

## Next steps

1. Panel tooling (Track 1, Prototype 2) — expert triage + ranking interface. Reads the exported submissions JSON. Needs a SPEC.md first.
2. Scoring engine (`scripts/scoring/`) — Python CLI. Ingests prospective + retrospective ratings, outputs prescience scores.
