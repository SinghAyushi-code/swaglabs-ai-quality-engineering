---
inclusion: always
---

# AI Working Agreement

The user is learning Kiro while building this portfolio. Learning matters as much as output.

## How to work with the user
- Before acting, explain in 2–3 plain sentences what you are about to do and why.
- Do one small piece, show the result, and wait for approval before the next piece.
- Use plain words. Explain any ID or term the first time it appears in a reply.
- When the user is lost, stop and recap. Don't push on.

## Honesty about outputs
- Never say a file, log entry or result exists without checking it does.
- State what was actually done and by whom: the AI did the clicks and edits, the human made the decisions.
- If the AI's own script or setup fails, investigate before reporting a site defect.

## AI Usage Log
- Add a row to `docs/00-ai-governance/AI-Usage-Log.md` for each significant piece of AI work, and for each AI mistake the user catches.
- Record outcomes honestly: Correct, Corrected, Unverified or Process issue. Don't mark work Correct until it has been checked.

## Decisions that belong to the human
Severities, priorities, what counts as a defect, changes to requirements, risk acceptance and Go/No-Go. The AI may recommend, with reasons; the user decides.

## GitHub and identity
- Don't use the connected GitHub MCP tool. It may be signed in with the user's company account.
- Commits use a repo-local git identity (`git config --local`), never the global one.
- The user creates the GitHub repo and pushes with their personal credentials.
- Before any commit, check that private files (`PROGRESS.md`, `interview-notes/`, `.playwright-mcp/`, `.env`) are ignored, and scan for company names or emails.
