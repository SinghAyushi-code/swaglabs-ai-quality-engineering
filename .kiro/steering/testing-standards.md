---
inclusion: always
---

# Testing Standards

## Requirements are the test basis
- The Product Backlog (`docs/01-requirements/Product-Backlog.md`) defines expected behaviour. The site does not.
- If the site differs from a story, **log a defect**. Never edit a story to match the site.
- Stories change only through a recorded Product Owner decision (`DEC-nnn`). Propose changes; don't make them.
- Text tagged `[PO-pending]` (register `OB-nn`) is observed behaviour, not a requirement. Don't use it as an expected result until it is decided.
- Don't decide whether something is a defect, or its severity. Present the evidence and let the user decide.

## Automation stack
- Playwright Test with TypeScript.
- Locate elements with `data-test` attributes first (the site provides them), then roles or labels. No CSS classes or XPath unless there is no alternative, with a comment explaining why.
- Page objects for pages; fixtures for login and test data.

## Test reliability
- **Every test starts in a fresh browser context** with no cookies or storage carried over. (Day 1: leftover state made every login fail and nearly produced a false defect, AI-011.)
- Wait for a condition (element visible, URL changed, count reached), never a fixed delay. (Day 1: a check read the page too early and reported a wrong count, AI-012.)
- Tests must not depend on each other or on run order.
- A test that fails, then passes on retry, is recorded as flaky, separately from defects.

## Data and secrets
- Read usernames and the password from environment variables (e.g. `SAUCE_PASSWORD`), even though SauceDemo's password is public. No credentials in code or committed files.
- Expected prices and totals come from the reference catalogue in story US-008, not from reading the page under test.

## Limits on the shared public demo
- No load, stress, performance or penetration testing, and no automated attacks (constraint CON-03).
- Security checks are passive and minimal: one request, one cookie edit, or browser devtools.
- Never bypass TLS certificate errors. Stop and report them.
