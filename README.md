# Prescience Benchmark

This is the root README for the project. It describes the folder structure, working conventions, and how to get started. It lives at `prescience-benchmark/README.md`.

## Recommended Folder Structure

```
prescience-benchmark/
│
├── CLAUDE.md                        ← Claude Code's primary context file (keep updated)
├── README.md                        ← Human-readable project overview
│
├── context/                         ← Read-only source material, never edited
│   ├── grant-proposal/              ← Coefficient Giving proposal + iterations
│   ├── stakeholder-notes/           ← Toby (Mantic), Swift Centre, partner feedback
│   ├── research-references/         ← Academic papers, prior art
│   └── interviews/                  ← Expert panel background, recruitment docs
│
├── prd/                             ← Product Requirements Document (living doc)
│   ├── PRD.md                       ← Main PRD — source of truth for what we're building
│   ├── decisions/                   ← Recorded design decisions with rationale (ADRs)
│   └── drafts/                      ← PRD iteration history
│
├── design/                          ← Architecture + methodology documentation
│   ├── evaluation-framework.md      ← Core prescience evaluation model
│   ├── scoring-rubrics/             ← How prescience is scored across tracks
│   └── diagrams/                    ← Mermaid / draw.io visual assets
│
├── prototypes/                      ← Active development work
│   ├── track-1-expert-panel/
│   │   ├── submission-interface/    ← Participant question submission (build first)
│   │   └── panel-tooling/          ← Expert triage + ranking interface (build second)
│   ├── track-2-market-signals/      ← Prediction market validation (build third)
│   └── track-3-ai-evaluation/       ← AI-based scalable evaluation (future phase)
│
├── data/                            ← All data assets
│   ├── question-corpus/             ← The candidate question bank
│   │   └── sample-corpus.json       ← Synthetic corpus for prototyping (generate first)
│   ├── ground-truth/                ← Validated prescience ground truth labels
│   ├── raw/                         ← Raw inputs from experts, markets, AI runs
│   └── processed/                   ← Cleaned, analysis-ready datasets
│
├── outputs/                         ← Finished deliverables only
│   ├── reports/
│   ├── presentations/
│   └── publications/
│
├── scripts/                         ← Shared utilities
│   ├── data-ingestion/
│   ├── scoring/                     ← Scoring engine lives here
│   └── analysis/
│
└── notes/                           ← Working notes, meeting logs, scratch
    └── YYYY-MM-DD-topic.md
```

---

## Git from Day One

Drive sync ≠ version control. Git is essential for Claude Code to see diffs and for safe experimentation.

```bash
cd prescience-benchmark
git init
echo "data/raw/" >> .gitignore
echo "*.env" >> .gitignore
echo "__pycache__/" >> .gitignore
echo ".DS_Store" >> .gitignore
git add .
git commit -m "Initial project structure"
```

---

## Working Pattern with Claude Code

Claude Code reads `CLAUDE.md` at the start of every session — it is the memory layer for the project.

**After each meaningful session:**
- Update `CLAUDE.md` with any decisions made
- Log a dated note in `/notes/YYYY-MM-DD-topic.md`
- Commit to Git

**For each prototype:**
- Write a `SPEC.md` inside the prototype folder before any code
- Each SPEC.md answers: inputs → processing → outputs → how to validate

**ADR naming:** `prd/decisions/001-ground-truth-strategy.md`, `002-...` etc. A short record of what was decided and why. Written once, read many times.

---

## Build Order

These are the four discrete prototypes, in priority order:

1. **Synthetic sample corpus** — `data/question-corpus/sample-corpus.json`  
   50 questions across six domains. Needed before anything else can be prototyped.

2. **Submission interface** — `prototypes/track-1-expert-panel/submission-interface/`  
   Web app for participants to submit questions during the tournament window.

3. **Panel tooling** — `prototypes/track-1-expert-panel/panel-tooling/`  
   Separate interface for expert panellists to triage and rank questions.

4. **Scoring engine** — `scripts/scoring/`  
   Python CLI module: ingests prospective + retrospective ratings, outputs prescience scores and leaderboard.

5. **Market signal validator** — `prototypes/track-2-market-signals/`  
   Matches corpus questions against Metaculus/Manifold; extracts timing and activity signals.

---

## First Claude Code Session — Starter Prompt

> *"Read CLAUDE.md and README.md. Our first task has three parts: (1) Create the project folder structure as defined in README.md. (2) Initialise git with a .gitignore covering data/raw/, \*.env, \_\_pycache\_\_/, .DS\_Store. (3) Generate a synthetic sample corpus of 50 questions across six domains — Healthcare & Pandemic Preparedness, Government Policy & Governance, Military & Security, Economics & Finance, Technology & AI, Climate & Environment — using this JSON schema per question: `{id, question_text, domain_tags, impact_self_assessment, supporting_rationale, submitted_by, participant_type}`. Save to data/question-corpus/sample-corpus.json and commit everything."*

