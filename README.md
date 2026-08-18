# Agentic AI on GCP

This repo has all the docs for the Agentic AI on GCP course. It has two main parts: `Core/`, which stays the same across cohorts, and `Cohorts/`, which holds files for each new batch. Old or replaced files go into `Archive/`. `Course_Meta/` holds process artifacts (like the prompt log) from building this repo, not course content.

## Core/

Stores the fixed parts of the course, the ones that don't change per cohort.

- `Standard_Operating_Playbook.md` — the repeatable checklist for launching and running any cohort.
- `Curriculum.md` — the full week-by-week course plan (EPR Agentic AI, 8-week plan).
- `Learning_Outcomes.md` — the terminal outcomes students should hit by course end.
- `Modules/` — the single canonical copy of all 8 weekly modules. Each `Module_N/` has its own `README.md` (title + description), an `Objectives_Checklist.md`, and separate `Instructor/` and `Student/` subfolders so content doesn't mix audiences.

## Blueprints/

Cross-functional planning docs spanning product, GTM, finance, and infrastructure.

- `Course_Master_Blueprint.md` — master plan: curriculum outline, GTM strategy, pricing/financial controls.
- `Infrastructure_Environment_Specification.md` — GCP lab environment spec: tooling, datasets, compute tiers, cost estimates.
- `Launch_Distribution_Brief.md` — go-to-market brief: pitch, launch date, channels, campaign copy.

## Cohorts/

One folder per batch, e.g. `2027-C1/`. Each cohort folder has:

- `Marketing/` — campaign plan and budget for this cohort's push. Lead/admissions tracking lives in a separate repo, linked from `Marketing/README.md`.
- `Budget.md` — full cohort finance (campaign spend + overall P&L).
- `Students.md` — admissions, onboarding, progress, and feedback.
- `Operations.md` — schedules, faculty, and day-to-day logistics.
- `Content_Updates.md` — holistic summary of what changed vs. `Core/` for this cohort (curriculum, assessments, or module content).

## Archive/

Old cohort files and outdated docs, kept for reference.

## Project Structure — Bird's Eye View

A full map of every tracked file currently in this repo, one line each, for quick orientation. Gitignored paths such as `Curriculumdetailed.md`, `temp.md`, `Project/`, and Office docs are excluded.

```
Course_1/
├── README.md                                          — repo overview, folder guide, and structure map
├── .gitignore                                         — current ignore rules
│
├── Core/                                              — fixed course material, same across cohorts
│   ├── README.md                                       — what Core/ holds, one line each
│   ├── Standard_Operating_Playbook.md                  — repeatable checklist for launching and running cohorts
│   ├── Curriculum.md                                   — full course plan and module overview
│   ├── Learning_Outcomes.md                            — terminal outcomes students should reach by course end
│   ├── Modules/                                        — canonical collection of weekly module content
│   │   ├── README.md                                   — index of modules and topics
│   │   ├── Module_1/                                   — week 1 module content
│   │   ├── Module_2/                                   — week 2 module content
│   │   ├── Module_3/                                   — week 3 module content
│   │   ├── Module_4/                                   — week 4 module content
│   │   ├── Module_5/                                   — week 5 module content
│   │   └── Module_6/                                   — week 6 module content
│
├── Blueprints/                                        — planning docs: product, GTM, finance, infrastructure
│   ├── Course_Master_Blueprint.md                      — master plan: curriculum, GTM, pricing, sign-offs
│   ├── Infrastructure_Environment_Specification.md     — GCP lab spec: tooling, datasets, compute, cost
│   └── Launch_Distribution_Brief.md                    — GTM brief: pitch, launch date, campaign copy
│
├── Cohorts/                                           — one folder per delivered batch
│   ├── README.md                                       — what a cohort folder contains
│   └── 2027-C1/                                       — first 2027 cohort's planning and execution
│       ├── README.md                                   — scope and structure for this cohort
│       ├── Marketing/README.md                         — campaign plan and admissions/promotions notes
│       ├── Budget.md                                   — cohort finance, campaign spend, and P&L
│       ├── Students.md                                 — admissions, onboarding, progress, and feedback tracking
│       ├── Operations.md                               — schedules, faculty, and day-to-day logistics
│       └── Content_Updates.md                          — summary of what changed versus Core
│
├── Archive/                                           — old or superseded docs and cohort files, kept for reference
└── (no Course_Meta/ root folder in this branch)        — Course_Meta is not present at the repository root
```

