# Swag Labs: AI-Assisted Quality Engineering

> 🚧 **Work in progress**: a 7-day build, started November 2026. This README will be completed at the end.

This project shows how a QA / SDET Lead can use an AI assistant ([Kiro](https://kiro.dev)) across the whole software lifecycle: requirements, test strategy, automation, CI/CD and release readiness. It also shows how the AI's work was checked and governed.

The system under test is [SauceDemo](https://www.saucedemo.com), a public demo shop owned by Sauce Labs. It is treated here as the "Swag Labs" store that we own, so behaviour a practice site would call intended counts as a defect. This project is not affiliated with Sauce Labs.

## Progress

| Day | Phase | Status |
|---|---|---|
| 1 | Requirements and early exploratory testing | ✅ Done |
| 2 | Repository setup and AI guardrails (Kiro steering) | ✅ Done |
| 3 | Test strategy and test design | Planned |
| 4 | Playwright + TypeScript framework | Planned |
| 5 | Automated tests | Planned |
| 6 | CI/CD pipeline and defect tracking | Planned |
| 7 | Release readiness report | Planned |

## Findings so far

- **35 user stories** in 5 areas (login, catalogue, cart, checkout, order confirmation), reverse-engineered by AI and reviewed by a human acting as QA Lead and Product Owner.
- **18 defects** from early exploratory testing (6 rated Critical), logged against the stories as written. For example, the order confirmation page can be opened without placing an order, and a different user on the same browser inherits the previous user's cart.
- **19 AI Usage Log entries**, recording where the AI was right, where it was corrected, and where its way of working caused problems.

The exploratory testing was carried out by Kiro driving a real browser (Playwright MCP). The decisions were made by a human: what counted as a defect, the severities, the priorities and the requirement changes.

## Where to look

| What | File |
|---|---|
| Project goal | [docs/project-charter.md](docs/project-charter.md) |
| User stories | [docs/01-requirements/Product-Backlog.md](docs/01-requirements/Product-Backlog.md) |
| Exploratory testing results and defects | [docs/01-requirements/Exploratory-Session-01.md](docs/01-requirements/Exploratory-Session-01.md) |
| QA review of the requirements | [docs/01-requirements/Product-Backlog-QA-Review.md](docs/01-requirements/Product-Backlog-QA-Review.md) |
| How the AI was checked | [docs/00-ai-governance/AI-Usage-Log.md](docs/00-ai-governance/AI-Usage-Log.md) |
| Standing rules for the AI | [.kiro/steering/](.kiro/steering/) |

## Author

Ayushi Singh
