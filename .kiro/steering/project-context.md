---
inclusion: always
---

# Project Context

## What this project is
A portfolio project showing AI-assisted Quality Engineering across the whole SDLC, led by a Senior SDET / QE Lead. The AI tool is Kiro.

- **System under test:** SauceDemo, https://www.saucedemo.com (public demo, owned by Sauce Labs, cannot be changed).
- **Framing:** we treat it as the "Swag Labs" online shop that we own. Behaviour a practice site calls intended (e.g. `problem_user`'s broken images) is a **defect** for us.
- **Special test users** (`problem_user`, `error_user`, `visual_user`, `performance_glitch_user`, `locked_out_user`) are simulated production conditions, not personas.
- **Author name** in all documents: Ayushi Singh.

## Where things live
| Path | Contents | Public? |
|---|---|---|
| `docs/project-charter.md` | Project goal and scope | Yes |
| `docs/00-ai-governance/AI-Usage-Log.md` | Every significant AI action, how it was checked, outcome | Yes |
| `docs/01-requirements/` | Business analysis, vision, backlog, QA review, exploratory session, assumption log | Yes |
| `.kiro/steering/` | These rules | Yes |
| `.kiro/specs/` | Kiro specs (from Day 3) | Yes |
| `PROGRESS.md` | 7-day plan, reminders, open decisions | **No** (private) |
| `interview-notes/` | Interview preparation | **No** (private) |
| `.playwright-mcp/` | Raw browser-tool logs | **No** |

## Start of every session
Read `PROGRESS.md` and tell the user, in a few lines, which day it is and what is due, including any reminders for that day.

## Key IDs
- Stories `US-nnn`, defects `DEF-nnn`, PO decisions `DEC-nnn`, observed behaviour awaiting decision `OB-nn`, assumptions `A-nn`, risks `R-nn`, AI log rows `AI-nnn`.
- Defects are recorded in `docs/01-requirements/Exploratory-Session-01.md` until a defect tracker is set up.
