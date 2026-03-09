# Prescience Benchmark — Product Requirements Document

**Version**: 0.1 (First Draft)  
**Status**: In Review  
**Owner**: Edward Strickland / Swift Centre for Applied Forecasting  
**Partners**: Mantic (AI forecasting), Electric Twin (AI evaluation)  
**Last Updated**: 2026-03-04  

---

## Open Questions (as of v0.1)

- [x] **OQ-1**: Panel size → **10–20 experts** (see Section 6.1)
- [x] **OQ-2**: Maximum questions per participant → **50** (see Section 5.1)
- [x] **OQ-3**: Participant eligibility → **Open to all**, with panel scaling implications (see Section 5.1)
- [x] **OQ-4**: Track 2 market signals → **In scope for pilot** (see Section 8.5)
- [x] **OQ-5**: Participant type tracking → **Tracked in v1** (see Section 4.3)
- [x] **OQ-6**: Freeze period → **Six months** (see Section 4.2)
- [x] **OQ-7**: Panel agreement threshold → **No minimum threshold; agreement is a research signal** (see Section 6.4)

Remaining open questions:

- [ ] **OQ-8**: Aggregation function for participant score — mean vs. Top-K mean (see Section 7.2)
- [ ] **OQ-9**: Whether supporting rationale submitted by participants is visible to panellists at prospective stage, retrospective stage, or both

---

## 1. Problem Statement

Forecasting infrastructure has matured significantly. Platforms like Metaculus, Manifold, and Good Judgment Open can aggregate well-calibrated probability estimates on defined questions. But this infrastructure addresses only the *downstream* problem: once a question has been identified as important, how likely is each possible outcome?

The *upstream* problem — determining which questions deserve forecasting attention in the first place — remains almost entirely unaddressed. In practice, analysts and organisations face information overload: thousands of potential uncertainties compete for limited attention. The questions that get asked are disproportionately the questions that are already prominent in discourse. Genuinely novel, decision-relevant uncertainties are systematically under-surfaced.

This upstream failure has real consequences. Crises are not predicted because the right questions were never asked. Decisions are made without awareness of the uncertainties that most warranted investigation. Forecasting systems are applied to the wrong questions.

**Prescience** — the ability to identify decision-relevant uncertainties *before* they become broadly recognised — is the skill this failure demands. It is currently unmeasured. No benchmark exists for it. We do not know which individuals, organisations, or AI systems possess it, how it can be developed, or whether it can be improved by AI assistance.

The Prescience Benchmark is the first systematic attempt to measure this skill.

---

## 2. Definition of Prescience

Prescience, as defined for this benchmark, is:

> **The ability to identify questions about the future that are both high-stakes and currently under-recognised, before that under-recognition becomes obvious.**

This is distinct from forecasting in a precise and important way:

| | Prescience | Forecasting |
|---|---|---|
| **Task** | Which questions deserve attention? | What is the probability of X? |
| **Input** | Open-ended — no question predefined | A defined, resolvable question |
| **Output** | A ranked set of questions | A probability estimate |
| **Skill** | Noticing what is being missed | Calibration under uncertainty |
| **Failure mode** | Asking the wrong questions | Being miscalibrated on the right ones |

A prescient question has two properties simultaneously:
1. **High stakes**: If answered, it would materially affect decisions by senior actors in the relevant domain.
2. **Under-recognised**: At the time of submission, it is not already receiving the attention its stakes warrant.

The benchmark measures prescience as an evaluable skill — it does not prescribe a method for developing it.

---

## 3. Evaluation Goals

A valid Prescience Benchmark must:

1. **Discriminate between participants**: Produce scores that meaningfully differentiate high-prescience from low-prescience submitters, not just reflect domain expertise or verbosity.

2. **Reward under-recognition, not just importance**: A question about a well-known risk should score lower than a neglected question of equal stakes — because the prescient act is the identification, not the importance.

3. **Use hindsight validation**: Ground truth should be re-assessed after a defined period, allowing empirical measurement of whether questions that were *initially* under-ranked turned out to *actually* matter.

4. **Be tractable for human expert panels**: The rating and ranking methodology must be feasible in terms of time and cognitive load at realistic submission volumes.

5. **Be transparent and reproducible**: Scoring methodology must be documented fully enough for external scrutiny, even though the human panel introduces non-determinism.

6. **Be extensible**: Architecture should support future additions (automated evaluation, rolling rounds, domain-specific tracks) without requiring a full redesign.

---

## 4. Tournament Structure

### 4.1 Overview

The pilot is structured as a single-round **tournament** — a bounded challenge with a defined start, submission window, freeze, and resolution period. It is not a rolling benchmark in v1, though the architecture should support that evolution.

### 4.2 Phases

```
Phase 1: Submission Window        Phase 2: Freeze Period           Phase 3: Resolution
─────────────────────────────     ───────────────────────────────  ──────────────────────────
Participants submit questions  →  Expert panel rates & ranks    →  Panel re-rates with
(up to N per participant)         questions (initial assessment)   hindsight; scores computed;
                                                                   leaderboard published via
                                                                   research paper
```

| Phase | Duration | Key Events |
|---|---|---|
| Submission Window | 4–6 weeks | Participant registration, question submission, corpus locked at deadline |
| Freeze Period | 6 months | Prospective expert panel rating; no new submissions; world events unfold |
| Resolution | 4–8 weeks | Retrospective expert re-rating; score computation; leaderboard generation |

### 4.3 Participants

Participants are individuals or teams who submit questions to the benchmark. In v1, participants may be:
- Human forecasters, researchers, or analysts
- AI systems (LLMs generating questions)
- Human-AI hybrid teams

**Participant type is tracked as a variable in v1.** This allows the research output to compare prescience performance across human, AI, and hybrid submitters — one of the most scientifically interesting dimensions of the benchmark.

---

## 5. Question Corpus

### 5.1 Source

All questions in the benchmark corpus come from participants. There is no externally-seeded question bank in v1.

Each participant may submit up to **50 questions**. This cap is a hard limit per submission round.

**On participant eligibility:** The tournament is open to all. This creates a direct scaling challenge. With open participation and 50 questions per participant, corpus size grows linearly:

| Participants | Max questions | Panel burden (est. at 30–60 sec/question per domain) |
|---|---|---|
| 10 | 500 | ~4–8 hrs per panellist |
| 20 | 1,000 | ~8–16 hrs per panellist |
| 50 | 2,500 | Not feasible without pre-filtering |

The practical implication: **panel size must scale with participant count.** The working ratio is approximately 1–2 additional panellists per 10 participants beyond the first cohort. The triage-then-rank workflow (Section 6.3) is essential for managing this load. The research team should monitor registration numbers during the submission window and expand the panel proactively if needed.

The 50-question cap incentivises participants to concentrate their submissions in areas where they have genuine insight rather than submitting across all domains indiscriminately. This improves question quality and reduces noise in the corpus.

### 5.2 Question Requirements

A valid benchmark question must:
- Be **genuinely uncertain** — not already resolvable with available information
- Be **forward-looking** — about something that could unfold within a 6–18 month window
- Be **domain-relevant** — plausibly within the scope of one or more expert panel domains
- Be **specific enough to be rankable** — not so broad as to be trivially important (e.g. "will AI cause problems?" is not a valid question)
- Be **novel** — not a duplicate or near-duplicate of another submission

Questions failing these criteria are filtered during the intake process (see Section 8).

### 5.3 Question Format

Each submitted question includes:
- **Question text** (required): The uncertainty, phrased as a question
- **Domain tag(s)** (required): One or more domain areas from a controlled list (e.g. Healthcare, Government Policy, Military & Security, Economics, Technology, Climate & Environment)
- **Impact self-assessment** (optional metadata, not scored): Low / Medium / High / Transformative — participant's own view of potential stakes
- **Supporting rationale** (optional, max 200 words): Why the participant believes this is under-recognised

The supporting rationale is visible to the expert panel. It may help experts interpret ambiguous questions but should not substitute for the question itself being clear.

### 5.4 Corpus for Prototyping

> **Claude Code Action**: A synthetic sample corpus of ~50 questions across domain areas should be generated for prototyping the submission interface and expert panel tooling. This corpus should be created early in development and committed to `/data/question-corpus/sample-corpus.json`. It does not represent real benchmark data.

---

## 6. Expert Panel

### 6.1 Composition

The expert panel is drawn primarily from Swift Centre's existing networks. Panellists are recruited to provide coverage across major impact domains:

- Healthcare & Pandemic Preparedness
- Government Policy & Governance
- Military & Security
- Economics & Finance
- Technology & AI
- Climate & Environment

**Panel size: 10–20 experts for the pilot**, with 2–3 per domain area to allow inter-rater reliability measurement. Panel size should scale with participant count (see Section 5.1).

The target time commitment per panellist is approximately **half a day per rating stage** (prospective and retrospective), spread across multiple sessions where needed. At 30–60 seconds per question, 50 domain-routed questions takes roughly 30–50 minutes — well within a half-day. Sessions should be documented to allow researchers to assess whether split-session rating introduces drift between sessions.

Panellists are not expected to rate questions outside their primary domain(s). Domain routing is handled during the expert panel tooling phase (see Section 8.2).

### 6.2 Rating Criterion

The expert panel uses a **single, anchored 5-point rating scale** applied at both the **prospective** (initial, at submission close) and **retrospective** (hindsight, at end of freeze period) stages.

---

**Prospective Rating Criterion** *(applied at submission close)*:

> *"As a trusted advisor to a senior decision-maker in your domain: how urgently should this question receive serious investigation in the next six months?"*

| Score | Label | Anchor Description |
|---|---|---|
| 5 | **Critical** | Should be on a minister's / senior executive's desk now. Major decisions hinge on this and it is not getting the attention it warrants. |
| 4 | **High** | A well-run organisation in this domain should be actively investigating this. Currently under-resourced relative to its importance. |
| 3 | **Moderate** | Worth tracking. Not urgent, but neglecting it could cause meaningful problems over the medium term. |
| 2 | **Low** | Marginal value. Either low stakes, already well-covered, or both. |
| 1 | **Negligible** | Not a meaningful uncertainty. Trivially known, already resolved, or not decision-relevant. |

---

**Retrospective Rating Criterion** *(applied at end of freeze period)*:

> *"In hindsight, how important was it that this question received serious investigation six months ago?"*

Same 5-point scale. Past tense. Experts now have knowledge of what unfolded during the freeze period.

---

**Rationale for this criterion design:**

The "trusted advisor" framing implicitly encodes both dimensions of prescience (stakes + under-recognition) without requiring experts to score them separately. A good advisor would not flag a question their decision-maker already knows about — so high-recognition questions are naturally downweighted. Simultaneously, trivial questions fail the "major decisions hinge on this" test. The framing is also domain-agnostic, allowing a consistent criterion across panellists from different fields.

### 6.3 Triage + Rank Workflow

Given that at scale (e.g. 20 participants × 50 questions = 1,000 questions), full stack-ranking is cognitively infeasible, the panel workflow is split into two stages:

**Stage 1 — Triage (all questions):**
Each expert rates all questions in their assigned domain(s) on the 5-point scale. Target time is 30–60 seconds per question — the expert panel UX should display this guidance explicitly to calibrate effort and keep ratings consistent across panellists. For 50–100 domain-routed questions, this is approximately 1–2 hours per panellist, ideally spread across multiple sessions.

**Stage 2 — Fine-grained ranking (top cluster only):**  
All questions rated 4 or 5 by any panellist are surfaced for within-cluster forced ranking. Experts rank their top ~20–30 questions within this shortlist. Statistical aggregation across panellists produces the final ordered ranking.

This design concentrates expert effort on the questions that matter most while keeping the triage stage tractable.

### 6.4 Inter-Rater Reliability as a Research Signal

With 2–3 panellists per domain, inter-rater reliability is computed for every question (e.g. Krippendorff's alpha, or intraclass correlation). **No minimum agreement threshold is enforced** — instead, agreement data is treated as a primary research output of the benchmark.

This matters because the benchmark is, among other things, investigating whether prescience can be reliably evaluated by humans — particularly prospectively. It is genuinely uncertain whether expert panels can agree on which questions are under-recognised before events resolve. High prospective disagreement that converges retrospectively would itself be a significant finding: it would suggest that prescience is only legible in hindsight, which has direct implications for benchmark design in future rounds.

The following agreement statistics are computed and published as part of the research output:
- Prospective inter-rater agreement (per domain and overall)
- Retrospective inter-rater agreement (per domain and overall)
- Prospective-to-retrospective agreement shift — did panels converge after the freeze period?
- Questions with highest disagreement at both stages (published as a qualitative discussion)

---

## 7. Scoring Methodology

### 7.1 Prescience Score for a Question

For each question *q* submitted by participant *p*:

```
Prescience_score(q) = Retrospective_rating(q) − Prospective_rating(q)
```

Where ratings are the aggregated panel scores (median or mean across panellists — see OQ-8).

| Score Range | Interpretation |
|---|---|
| +3 to +4 | High prescience — severely underrated initially, proved critical |
| +1 to +2 | Moderate prescience — somewhat ahead of consensus |
| 0 | Neutral — correctly rated initially |
| −1 to −2 | Overrated — submitted as urgent but turned out not to matter |
| −3 to −4 | Significant overrating — false urgency |

### 7.2 Participant Score

Each participant's overall benchmark score is an aggregate of their question-level prescience scores:

```
Participant_score(p) = f(Prescience_score(q) for all q submitted by p)
```

The aggregation function *f* is unresolved (OQ-8). Candidates:

- **Mean**: Rewards consistent performance across all submissions
- **Top-K mean**: Rewards the best K questions — allows for a "hit-based" model where a few very prescient questions count most
- **Weighted mean with impact self-assessment**: Questions self-tagged as High/Transformative impact are upweighted if their prescience score confirms the assessment

> The Top-K mean is recommended for v1 as it mirrors how prescience operates in practice — a single highly prescient insight can be genuinely valuable even if other submissions are unremarkable.

### 7.3 Leaderboard

The leaderboard is published as part of the research paper output at the end of the Resolution phase. It is not published in real-time — participants do not know their scores during the Freeze Period.

The leaderboard includes:
- Participant rank
- Overall score
- Number of questions submitted
- Number of questions in the high-prescience cluster (score ≥ +2)
- Participant type (human / AI / hybrid) — if OQ-5 is resolved affirmatively

---

## 8. System Features

### 8.1 Submission Interface

A web-based interface for participants to submit questions during the Submission Window.

**Requirements:**
- Participant registration and authentication
- Question entry form (text, domain tags, impact self-assessment, optional rationale)
- Duplicate/near-duplicate detection (fuzzy matching against existing corpus)
- Submission count tracking (enforces per-participant cap)
- Submission confirmation and receipt
- Admin view: full corpus management, participant overview, export to JSON/CSV

> **Claude Code Action**: This is the first prototype to build. Start with `/prototypes/track-1-expert-panel/submission-interface/`. Use the synthetic sample corpus from Section 5.4 to populate test data. Scaffold as a simple web app (framework to be decided in ADR-002).

### 8.2 Expert Panel Tooling

A separate interface for expert panellists to complete triage ratings and fine-grained ranking.

**Requirements:**
- Panellist login (separate from participant login)
- Domain-routed question queue — each panellist only sees questions in their assigned domain(s)
- Triage view: question displayed with rating scale (1–5), rationale visible on expand
- Progress tracking: how many questions rated, how many remaining
- Fine-grained ranking view: drag-and-drop or pairwise comparison for top-cluster questions
- Admin view: rating progress per panellist, inter-rater agreement stats, export

> **Claude Code Action**: Second prototype, after submission interface. Lives in `/prototypes/track-1-expert-panel/panel-tooling/`. Can share component library with submission interface.

### 8.3 Scoring Engine

A backend module that computes prescience scores and leaderboard from initial and hindsight panel ratings.

**Requirements:**
- Ingests initial and hindsight rating exports
- Computes per-question prescience scores
- Applies aggregation function to produce per-participant scores
- Generates leaderboard output (JSON + formatted report)
- Handles missing data (questions not rated by sufficient panellists)
- Produces inter-rater reliability statistics

> **Claude Code Action**: Build as a standalone Python module in `/scripts/scoring/`. Should be runnable from CLI with config file. Design for testability — unit tests against known inputs/outputs.

### 8.4 Question Corpus Management

Internal tooling for the research team to manage the corpus: review submissions for validity, flag duplicates, assign domain tags where missing, export corpus snapshots.

**Requirements:**
- Admin-only
- Review queue: flag / approve / reject individual questions
- Duplicate detection report
- Bulk export (JSON, CSV)
- Corpus snapshot versioning (so the exact corpus at freeze time is preserved)

### 8.5 Track 2 — Market Signal Validation

Track 2 provides external validation of the expert panel's prescience scores by checking whether submitted questions correlate with activity on live prediction markets.

**Rationale:** If a question submitted early in the tournament subsequently appears on Metaculus, Manifold, Polymarket, or similar platforms — especially if it gains significant trading volume or resolution activity — this is an independent signal that the question was prescient. It confirms the expert panel's retrospective rating using market behaviour rather than panel opinion alone.

**Approach:**

1. At resolution phase, the full question corpus is searched against active and recently-resolved questions on major prediction market platforms (Metaculus, Manifold, Polymarket, Good Judgment Open).
2. Matches are identified using semantic similarity (fuzzy text matching + embedding-based search).
3. For matched questions, the following signals are extracted:
   - Whether a matching market question existed *before or after* the submission date (prior existence weakens the prescience claim; post-submission appearance strengthens it)
   - Market activity volume and resolution status
4. Market signal data is used as a **validation layer**, not a replacement for panel scoring. It can surface cases where the panel underrated a question that markets subsequently proved important.

**Limitations acknowledged in v1:**
- Not all important questions generate market activity — absence of a matching market is not evidence of low importance
- Market question framing may differ from benchmark question framing, requiring manual review for borderline matches
- Prediction market participation skews towards certain domains (technology, politics) and away from others (healthcare, military)

> **Claude Code Action**: Build as `/prototypes/track-2-market-signals/`. Core components: (1) an export of the question corpus in a format suitable for semantic search, (2) API integrations or scrapers for Metaculus and Manifold at minimum, (3) a matching report generator. This is a lower-priority prototype than Track 1 tooling.

The following are explicitly not being built for the pilot tournament:

- **Real-time scoring or leaderboard** — scores are only published post-resolution
- **Track 3 (AI Evaluation)** — automated evaluation layer is a future-phase feature
- **Public-facing results dashboard** — results are communicated via research paper, not a live platform
- **Mobile-optimised interfaces** — web-only, desktop-first is sufficient for v1
- **Multi-round rolling benchmark** — architecture should support this but it is not built in v1
- **Participant feedback portal** — participants do not receive individualised score breakdowns in v1 (only the published leaderboard)
- **API access** — no public API in v1

---

## 10. Success Criteria

### Pilot Tournament Success

The pilot is considered a success if:

1. At least **3 participants** complete a full submission set — even a single participant's submissions generate meaningful signal about prescience
2. At least **8 expert panellists** complete prospective and retrospective ratings
3. The scoring engine produces a **statistically discriminating leaderboard** — scores are not uniformly distributed
4. At least **one clearly prescient question cluster** is identified — questions underrated prospectively that proved important retrospectively
5. Inter-rater reliability data is sufficient to make a credible claim about whether expert panels can reliably evaluate prescience
6. The methodology is **documented sufficiently** for a research paper and for replication by third parties

### Conditions for Future Rounds

A second round is initiated if:

- Pilot produces statistically meaningful results worth building on
- At least one institutional partner (beyond Swift Centre) commits to participation
- Research paper is accepted or under review at a credible venue

---

## 11. Technical Principles

- **Python 3.11+** for all backend scripts and data processing
- **Type hints** on all function signatures
- **Config-driven** — no hardcoded paths, credentials, or parameters
- **Each track is a self-contained module** under `/prototypes/`
- **All data flows through defined schemas** — JSON schemas for question corpus, ratings, and scores to be defined before implementation begins
- **Git from day one** — commit after every meaningful unit of work
- **CLAUDE.md is the memory layer** — updated after every session with decisions made

---

## 12. Appendix: Key Terminology

| Term | Definition |
|---|---|
| **Prescience** | The ability to identify high-stakes, under-recognised questions before their importance becomes broadly apparent |
| **Prescience score** | The delta between a question's retrospective rating and its prospective rating |
| **Prospective rating** | Expert panel score assigned at submission close, before events resolve (also: *ex ante*) |
| **Retrospective rating** | Expert panel score assigned at end of freeze period, with knowledge of what unfolded (also: *ex post*) |
| **Question corpus** | The full set of questions submitted by participants for a given tournament round |
| **Freeze period** | The six-month period between corpus lock and retrospective re-rating, during which the world unfolds |
| **Triage phase** | First stage of expert panel process: 5-point rating of all domain-routed questions |
| **Rank phase** | Second stage: forced ranking of the top-rated question cluster |
| **Participant** | An individual, team, or AI system submitting questions to the benchmark |
| **Panellist** | A domain expert recruited to rate and rank questions |
| **Leaderboard** | Ranked list of participants by aggregate prescience score, published post-resolution |
| **ADR** | Architecture Decision Record — a short numbered document capturing what decision was made and why |
