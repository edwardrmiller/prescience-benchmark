# SPEC: Submission Interface
## Track 1 — Expert Panel / Prototype 1

---

## Purpose

A web form that allows tournament participants to submit questions during the submission window. Outputs a structured JSON file of submitted questions that feeds directly into the expert panel triage workflow.

This is a prototype for validating the submission UX and data schema — not a production multi-user system. Authentication, persistence, and concurrency are out of scope for v1.

---

## Inputs

- Participant fills in a form with the following fields:

| Field | Type | Required | Constraints |
|---|---|---|---|
| `question_text` | string | Yes | 20–300 characters |
| `domain_tags` | multi-select | Yes | 1–3 domains from the fixed list |
| `impact_self_assessment` | integer | No | 1–5 scale (labelled) |
| `supporting_rationale` | string | No | Max 200 words |
| `submitted_by` | string | Yes | Participant identifier |
| `participant_type` | select | Yes | Human / AI / Human-AI hybrid |

Fixed domain list (matches CLAUDE.md):
- Healthcare & Pandemic Preparedness
- Government Policy & Governance
- Military & Security
- Economics & Finance
- Technology & AI
- Climate & Environment

---

## Processing

1. Client-side validation on all required fields and constraints before submission.
2. On submit, append question to an in-memory list with an auto-generated `id` (`q001`, `q002`, …).
3. Display a running count: "X of 50 questions submitted."
4. Allow review/delete of submitted questions before export.
5. On export, serialise the full list to JSON matching the corpus schema.

---

## Outputs

A single JSON file: `submissions-YYYY-MM-DD.json`

Schema per question matches `data/question-corpus/sample-corpus.json`:
```json
{
  "id": "q001",
  "question_text": "...",
  "domain_tags": ["..."],
  "impact_self_assessment": 3,
  "supporting_rationale": "...",
  "submitted_by": "...",
  "participant_type": "Human"
}
```

---

## Stack

- **Single HTML file** — no build step, no framework dependencies
- Vanilla JavaScript for state management and validation
- Plain CSS for layout
- File download via `Blob` + `URL.createObjectURL` for JSON export

Rationale: The prototype needs to run locally without a server. A single-file approach lets any stakeholder open it in a browser without setup.

---

## How to Validate

1. Open `index.html` in any modern browser.
2. Submit 3–5 test questions across different domains.
3. Verify the running count updates correctly.
4. Verify client-side validation blocks submission of invalid inputs.
5. Click Export and confirm the downloaded JSON matches the corpus schema.
6. Load the exported JSON into `data/question-corpus/` and confirm it passes schema inspection.

---

## Out of Scope (v1)

- Server-side persistence
- Authentication / participant accounts
- Duplicate detection
- Deadline enforcement (submission window close)
- Admin view of all participants' submissions
