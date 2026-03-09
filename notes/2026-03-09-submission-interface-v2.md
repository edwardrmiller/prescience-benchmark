# 2026-03-09 — Submission Interface v2 (CSV upload)

## What changed

SPEC.md was updated to replace manual single-question entry with a CSV upload flow. The interface was rebuilt from scratch accordingly.

## What was built

- **index.html** replaced entirely. Single-file, no build step.
- Four-step layout: Download Template → Upload CSV → Validation Report → Review & Export.

## Key design decisions

- **CSV parser is hand-rolled** (no library) to keep the single-file constraint. Handles RFC-4180 quoting (escaped quotes, fields with commas/newlines).
- **Pipe separator for domain_tags** in CSV (`Economics & Finance | Technology & AI`). Split on `|`, trimmed, validated against the fixed domain list.
- **Validation report is per-row**: each CSV row gets an Accepted or Rejected badge with a specific human-readable reason. Multiple errors on one row are semicolon-joined.
- **50-question cap** enforced at parse time: rows beyond 50 get a rejection reason explaining why.
- **Drag-and-drop** supported on the upload zone in addition to click-to-browse.
- **Reset button** clears all state and returns to the upload step without requiring a page reload.
- `window._lastParsed` holds the last parse result so the export button can serialise it without re-reading the file.

## Open questions carried forward

- OQ-9: Should supporting rationale be visible to panellists at prospective stage, retrospective stage, or both? Determines what the panel tooling must display/hide.
- Should the CSV template include a second example row showing a deliberately invalid entry, to help participants understand validation rules? Useful for the real pilot.

## Next steps

1. Panel tooling (Track 1, Prototype 2) — SPEC.md first, then build.
2. Scoring engine (`scripts/scoring/`) — Python CLI.
