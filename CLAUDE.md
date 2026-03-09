# Prescience Benchmark — CLAUDE.md

## What We Are Building

The Prescience Benchmark is the first evaluation framework for measuring *prescience*: the ability to identify which questions deserve forecasting attention before that becomes obvious. This is distinct from forecasting in a precise and important way.

**Prescience** (this project): Which questions deserve attention? Upstream, open-ended, no question predefined.  
**Forecasting** (not this project): What is the probability of X? Downstream, requires a defined resolvable question.

We are measuring prescience as a skill — we are not prescribing a method for developing it. The benchmark is a research instrument, not an operational tool.

---

## Tournament Structure

The pilot runs as a single-round tournament with three phases:

1. **Submission Window** (4–6 weeks): Participants submit up to 50 questions each. Corpus locked at deadline.
2. **Freeze Period** (6 months): Expert panel completes prospective (ex ante) ratings. No new submissions. World events unfold.
3. **Resolution** (4–8 weeks): Expert panel completes retrospective (ex post) ratings. Scores computed. Leaderboard published via research paper.

---

## Ground Truth Strategy (Track 1)

Ground truth is established by a human expert panel using a **single anchored 5-point rating scale**, applied twice:

**Prospective criterion** (at submission close):
> "As a trusted advisor to a senior decision-maker in your domain: how urgently should this question receive serious investigation in the next six months?"

**Retrospective criterion** (at end of freeze period):
> "In hindsight, how important was it that this question received serious investigation six months ago?"

| Score | Label | Meaning |
|---|---|---|
| 5 | Critical | Should be on a minister's desk now. Major decisions hinge on this. |
| 4 | High | Well-run organisations should be actively investigating this. |
| 3 | Moderate | Worth tracking. Not urgent. |
| 2 | Low | Low stakes or already well-covered. |
| 1 | Negligible | Trivially known, already resolved, or not decision-relevant. |

**Panel workflow** — two stages to manage volume:
- **Triage**: Rate all domain-routed questions at 30–60 seconds each
- **Rank**: Forced ranking of the top-rated cluster (scores 4–5)

---

## Scoring

```
Prescience_score(question) = Retrospective_rating − Prospective_rating
```

A question rated 2 prospectively and 5 retrospectively = high prescience (+3). This is the pattern to reward: under-recognised at submission time, proved important in hindsight.

**Participant score** = aggregate of their questions' prescience scores. Aggregation function (mean vs. Top-K mean) is an open question — see OQ-8.

**Credit** goes to the **submitter** of the question, not to participants who ranked it highly.

---

## Expert Panel

- **Size**: 10–20 experts for the pilot; scales with participant count
- **Domains**: Healthcare & Pandemic Preparedness, Government Policy & Governance, Military & Security, Economics & Finance, Technology & AI, Climate & Environment
- **Source**: Swift Centre's existing networks
- **Routing**: Each panellist only sees questions in their assigned domain(s)
- **Inter-rater agreement** is tracked as a research signal, not used as a quality gate. Whether humans can reliably evaluate prescience prospectively is itself an open research question.

---

## Question Corpus

- **Source**: Participants only — no externally-seeded questions in v1
- **Cap**: 50 questions per participant
- **Participant types tracked**: Human / AI / Human-AI hybrid
- **Question format**: question_text, domain_tags, impact_self_assessment (optional metadata), supporting_rationale (optional, max 200 words)
- **Sample corpus for prototyping**: `data/question-corpus/sample-corpus.json` (50 synthetic questions)

---

## Three-Track Architecture

| Track | Purpose | Pilot status |
|---|---|---|
| Track 1 — Expert Panel | Ground truth via prospective + retrospective ratings | In scope — build first |
| Track 2 — Market Signals | External validation via prediction market matching (Metaculus, Manifold) | In scope — build third |
| Track 3 — AI Evaluation | Scalable automated evaluation layer | Deferred to future round |

---

## Open Questions

- **OQ-8**: Aggregation function for participant score — mean vs. Top-K mean
- **OQ-9**: Whether participant supporting rationale is visible to panellists at prospective stage, retrospective stage, or both

---

## Build Order

1. `data/question-corpus/sample-corpus.json` — synthetic corpus, needed first
2. `prototypes/track-1-expert-panel/submission-interface/` — participant question submission
3. `prototypes/track-1-expert-panel/panel-tooling/` — expert triage + ranking interface
4. `scripts/scoring/` — Python CLI scoring engine
5. `prototypes/track-2-market-signals/` — prediction market validator

Each prototype gets a `SPEC.md` before any code is written.

---

## Key Stakeholders

- **Swift Centre for Applied Forecasting** — project lead
- **Mantic** — AI forecasting expertise; primary contact: Toby
- **Electric Twin** — AI evaluation systems

---

## Coding Conventions

- Python 3.11+ for all data processing and analysis
- Type hints on all function signatures
- No hardcoded paths — use config files
- Each track is a self-contained module under `/prototypes/`
- All data loading goes through `/scripts/data-ingestion/`
- JSON schemas for question corpus, ratings, and scores must be defined before implementation begins
- Commit after every meaningful unit of work

---

## Terminology

| Term | Definition |
|---|---|
| **Prescience** | The ability to identify high-stakes, under-recognised questions before their importance becomes broadly apparent |
| **Prescience score** | Retrospective rating minus prospective rating for a given question |
| **Prospective / ex ante** | Before the freeze period; without knowledge of how events unfolded |
| **Retrospective / ex post** | After the freeze period; with knowledge of what unfolded |
| **Freeze period** | The six-month window between corpus lock and retrospective re-rating |
| **Triage phase** | First stage of panel process: 5-point rating of all domain-routed questions |
| **Rank phase** | Second stage: forced ranking of the top-rated cluster |
| **ADR** | Architecture Decision Record — short numbered doc in `prd/decisions/` capturing what was decided and why |

---

## End of Session Checklist

Run these steps at the end of every Claude Code session, in order:

1. **Update Session Log** — add a row to the table below with today's date and a one-line summary of what was decided or built.
2. **Write a dated note** — create `/notes/YYYY-MM-DD-topic.md` capturing key decisions, open questions, and next steps from the session.
3. **Commit everything** — `git add . && git commit -m "brief description of session work"`

---

## Session Log

Update this section after each Claude Code session with the date and what was decided or built.

| Date | Summary |
|---|---|
| 2026-03-09 | Project initialised. PRD v0.1 complete. Folder structure created. |
| 2026-03-09 | Added End of Session Checklist to CLAUDE.md. Built submission interface prototype: SPEC.md + single-file index.html with validation, domain tagging, impact rating, and JSON export. |
| 2026-03-09 | Rebuilt submission interface to CSV upload model per updated SPEC.md. Template download, client-side CSV parse/validate, per-row validation report, review table, JSON export. |
| 2026-03-09 | Built panel tooling prototype (Track 1, step 2): SPEC.md + single-file index.html. Four-screen flow: setup → triage → rank → export. Domain filtering, random shuffle, auto-advance on rating, drag-and-drop ranking with ↑↓ fallback. |
| 2026-03-09 | Created GitHub repo (edwardrmiller/prescience-benchmark). Enabled GitHub Pages from root of main. Added index.html landing page. Pages URL: https://edwardrmiller.github.io/prescience-benchmark/ |
| 2026-03-09 | Added supporting rationale display to panel tooling triage card. Updated SPEC.md. Fixed upload zone border rendering on submission interface. Added debug sample corpus download link to panel tooling. |
