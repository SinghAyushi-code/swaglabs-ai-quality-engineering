# Product Backlog QA Review (PB-001 v0.1)

## Review Control

| Field | Value |
|---|---|
| Document ID | QAR-001 |
| Artefact reviewed | [Product Backlog PB-001 v0.1](Product-Backlog.md) |
| Reviewer role | Senior QA Lead |
| Review type | Static review of requirements (no SUT execution) |
| Review date | 2026-11-12 |
| AI involvement | Review drafted by AI (Kiro). **The same AI drafted PB-001, so this is a self-review and not independent.** A human reviewer must confirm the findings before PB-001 is approved. Log both in AI_Usage_Log. |
| Outcome | **Not Ready for sprint planning.** Approve as v0.1 only after the Critical and Major findings are resolved or explicitly accepted by the PO. |

### Severity Scale

| Severity | Meaning |
|---|---|
| Critical | Affects the Go/No-Go decision, exposes customer accounts, or lets a wrong charge go undetected |
| Major | The story cannot be tested as written, or a test could pass while the business need fails |
| Minor | Wording, consistency or traceability; low risk of a wrong test |

Items marked **[Validate]** rely on prior knowledge of SauceDemo and must be confirmed in the exploratory session (OI-01).

### Summary

| Category | Critical | Major | Minor | Total |
|---|---|---|---|---|
| 1. Ambiguous requirements | 1 | 7 | 4 | 12 |
| 2. Missing requirements | 1 | 6 | 3 | 10 |
| 3. Missing edge cases | 1 | 9 | 3 | 13 |
| 4. Potential defects in the requirements | 2 | 5 | 3 | 10 |
| 5. Security concerns | 3 | 5 | 1 | 9 |
| 6. Testability concerns | 1 | 7 | 1 | 9 |
| **Total** | **9** | **39** | **15** | **63** |

---

## 1. Ambiguous Requirements

| ID | Story | Finding | Sev | Recommendation |
|---|---|---|---|---|
| QA-AMB-01 | US-027 | No rounding rule is defined. "Rounded to 2 decimal places" doesn't say half-up or half-even, or whether tax is rounded per line or on the item total. Different rules give different totals at the .005 boundary. | Critical | State the rule: tax = round-half-up(item total × rate, 2 dp), calculated on the item total. Confirm it against the SUT (A-05). |
| QA-AMB-02 | US-024 | The AC accepts two different outcomes: "cannot start checkout, **or** I am told the cart is empty". A tester can't tell which one is correct. | Major | Choose one. Recommended: Checkout is disabled when the cart is empty. |
| QA-AMB-03 | US-021 AC2 | Re-login behaviour is left as "a PO decision". An AC that has no expected result can't be tested. | Major | PO decides before the story goes into a sprint (OI-02). Until then, remove AC2 from the story's Definition of Done. |
| QA-AMB-04 | US-029 | "Return to a store page" doesn't name the page. US-025 returns to the Cart, so the two cancel paths are inconsistent. | Major | Name the destination. If the SUT returns to Products, decide whether that is acceptable or a defect. |
| QA-AMB-05 | US-010 | "Alphabetical order" isn't defined for case, punctuation or locale. "Test.allTheThings() T-Shirt (Red)" contains punctuation **[Validate]**. | Major | Define it as case-insensitive, ordered by Unicode code point (or by locale collation, en-GB). Add the expected order of the 6 products as an oracle. |
| QA-AMB-06 | US-011 | No tie-break rule for equal prices. Two products are believed to cost $15.99 **[Validate]**. AC3 only covers "while I remain on the page". What happens after visiting a detail page and coming back isn't stated. | Major | Define a secondary sort (name A–Z). State whether the sort order persists across navigation. |
| QA-AMB-07 | US-008 AC3 | "Its own image" has no oracle. A tester can't tell a correct image from a plausible wrong one. | Major | Add a reference table mapping each product to its image file (see QA-TST-03). |
| QA-AMB-08 | Cross-cutting | "Behave as in the acceptance criteria above" and "fails silently" give no expected result per test user. `performance_glitch_user` has no pass threshold. | Major | Add an expected-result matrix per condition. For performance, set a functional threshold, e.g. "Products page shown within 10 s; record the actual time". |
| QA-AMB-09 | US-005 AC2, US-030 AC3 | "Tells me I must log in" and "what happens next" are vague. | Minor | Replace with exact text once it has been validated. |
| QA-AMB-10 | US-001 | "Valid username and password" doesn't say which accounts. `problem_user`, `error_user` and the others are also valid. | Minor | Point to a test-account table that gives each account's expected result. |
| QA-AMB-11 | US-006, US-015 | "Any store page" is used without a list of pages. | Minor | Add one page inventory (the 6 routes, plus menu availability on each) and refer to it. |
| QA-AMB-12 | US-027 vs SUT | The story says "subtotal". The SUT is believed to label it "Item total" **[Validate]**. | Minor | Use the SUT label so the AC matches the screen. |

---

## 2. Missing Requirements

| ID | Area | Finding | Sev | Recommendation |
|---|---|---|---|---|
| QA-MIS-01 | Checkout | No checkout step ordering. Nothing stops a logged-in customer opening the overview or complete page directly, skipping information entry or reaching confirmation with an empty cart. | Critical | New story: "Checkout steps can only be reached in order". AC: a direct URL to a later step redirects to the correct earlier step. |
| QA-MIS-02 | Login | No brute-force protection or lockout after repeated failed attempts. | Major | Add a requirement (e.g. lock after 5 failures) or record it as an accepted gap. It cannot be tested on the SUT (CON-03); see QA-SEC-04. |
| QA-MIS-03 | Session | No session timeout or expiry requirement. | Major | Define the idle timeout and the behaviour when a session expires mid-checkout. SauceDemo's session cookie is believed to expire after about 10 minutes **[Validate]**. |
| QA-MIS-04 | Login | No requirement for where the customer goes after logging in from a deep link (US-005). There's also no requirement for an already logged-in customer who opens the login page. | Major | Add ACs for both cases. |
| QA-MIS-05 | Checkout | No field validation beyond "required": no maximum length, allowed characters or postal code format. This conflicts with the input-validation rule in the security policy. | Major | Define the limits per field. For postal code, decide which formats are supported (US ZIP, UK postcode). |
| QA-MIS-06 | Catalogue | No behaviour for an invalid product ID on the detail page (e.g. `?id=99`). SauceDemo is believed to show "ITEM NOT FOUND" **[Validate]**. | Major | Add an AC. Expected: a clear not-found message and a route back to Products. |
| QA-MIS-07 | Non-functional | No accessibility requirement (keyboard use, labels, error announcements, contrast). | Major | Add a cross-cutting NFR at WCAG 2.2 AA, or record it as an accepted gap for MVP 1. |
| QA-MIS-08 | Non-functional | Supported browsers and screen sizes aren't stated. | Minor | Define the browser and viewport matrix, for example the latest Chromium, Firefox and WebKit at desktop and mobile widths. |
| QA-MIS-09 | Menu | MVP-15 (About and social links) and the "All Items" and close-menu actions have no story. That breaks traceability from BV-001 scope. | Minor | Add one Low story, or mark MVP-15 explicitly as dropped. |
| QA-MIS-10 | KPIs | KPI-01 to KPI-03 need event tracking (login, checkout start, confirmation, lockout), but no story provides it. | Minor | Add an analytics story for Release 2. Note that MVP 1 cannot produce a KPI baseline. |

---

## 3. Missing Edge Cases

| ID | Story | Edge case | Sev | Recommendation |
|---|---|---|---|---|
| QA-EDG-01 | US-030 | Browser Back or a refresh after Finish, and a double-click on Finish. Either could place a duplicate order or cause a confusing re-submission. | Critical | Add ACs: Back from confirmation doesn't re-place the order, and a double-click places one order. |
| QA-EDG-02 | US-022, US-023 | Whitespace-only values ("   ") in the name and postal code fields. | Major | AC: whitespace-only counts as empty. |
| QA-EDG-03 | US-022 | Very long input, Unicode names (O'Brien, Zoë, 李), emoji, leading-zero and alphanumeric postal codes. | Major | Add boundary and equivalence cases once the QA-MIS-05 limits exist. |
| QA-EDG-04 | US-001, US-002 | Username case ("Standard_User") and leading or trailing spaces; submitting with the Enter key. | Major | State whether usernames are case-sensitive and whether input is trimmed. |
| QA-EDG-05 | US-012, US-016 | A rapid double-click on Add to cart. It should not create two lines or a badge count of 2. | Major | Add an AC. |
| QA-EDG-06 | US-016, US-019 | Two browser tabs: add an item in one tab and view the cart in the other. | Major | State the expected sync behaviour. At minimum, nothing is lost or duplicated. |
| QA-EDG-07 | US-016 | Boundaries: 0 → 1 (badge appears), all 6 items, and 6 → 0 (badge disappears). | Major | Make these explicit test conditions. |
| QA-EDG-08 | US-019 | Closing the tab or browser and reopening it while still logged in. | Major | State the expected behaviour (cart kept or cleared). |
| QA-EDG-09 | US-022, US-025 | Refreshing the information or overview page; going Back from the overview to the information page; whether entered details are kept or cleared after Cancel. | Major | Add ACs for each. |
| QA-EDG-10 | US-020 | Reset App State while on the Cart or a checkout page. It's also unclear whether reset clears the sort order. | Major | Add ACs. |
| QA-EDG-11 | US-004 | A locked user enters a **wrong** password. Does the lockout message still appear? See QA-SEC-02. | Minor | State the expected message. |
| QA-EDG-12 | US-011, US-010 | Sorting, then adding an item: the sort order and button states stay correct. | Minor | Add as a test condition. |
| QA-EDG-13 | US-027 | Tax values that fall exactly on .005. | Minor | Cover with the exhaustive subset test (QA-TST-04). |

---

## 4. Potential Defects in the Requirements

| ID | Story | Finding | Sev | Recommendation |
|---|---|---|---|---|
| QA-DEF-01 | US-007 | This High story is known to fail and cannot be fixed (CON-01). SC-07 requires 0 open High defects on Must items, or written PO acceptance. US-007 doesn't trace to any MVP-xx item, and unlike US-024 it isn't flagged as a new requirement. | Critical | Flag it as new scope that needs PO approval. Record the PO's written risk acceptance against SC-07 before Go. |
| QA-DEF-02 | US-004 vs US-002 | Contradiction. US-002 AC2 hides whether the username or password was wrong. US-004 shows a lockout message when the correct password is entered, which confirms the account exists and the password is correct. | Critical | Decide which rule wins. Security recommendation: show the lockout message only after successful authentication, and log it as a residual risk. See QA-SEC-02. |
| QA-DEF-03 | US-023, US-025, US-029 | Priority doesn't match the release criteria. They are Medium (Should), but SC-04 makes checkout validation a release gate (3 of 3 fields). | Major | Raise US-023 to High, or change SC-04. Priority and release gates must agree. |
| QA-DEF-04 | US-021 vs US-006 | US-006 is High "so the next person cannot use my account", but the related privacy risk in US-021 is only Medium. | Major | Raise US-021 to High or justify the difference. R-11 involves possible exposure of personal data. |
| QA-DEF-05 | US-033 | A Won't item (OOS-04) sits in the MVP 1 backlog with ACs that can't be tested in MVP 1. It's counted in the totals as one of the 4 Low stories. The priority scale merges "Could" and "Won't" into Low. | Major | Move it to a "Release 2 candidates" section, exclude it from MVP totals, and give "Won't" its own priority value. |
| QA-DEF-06 | US-008 AC1 | It hard-codes "6 products" before A-06 is validated. | Major | Use "every product in the approved catalogue reference table" (QA-TST-03). |
| QA-DEF-07 | US-022 | The "So that" says the order is matched to the stored delivery address, but no AC checks the postal code against the account address. The business value depends on an untested behaviour (A-03). | Major | Add the AC under A-03, or reword the benefit to match what is actually checked. |
| QA-DEF-08 | US-002 | It traces to SC-03, but SC-03 covers redirects and locked accounts, not invalid credentials. | Minor | Correct the trace or extend SC-03. |
| QA-DEF-09 | BV-OBJ-06 | No story owns the degraded conditions, so they can't be estimated or tracked on a board. | Minor | Turn the cross-cutting table into one enabler story per condition, or into Definition of Done items. |
| QA-DEF-10 | US-019 | High priority, but it traces to BV-CAP-06, which is mainly Could scope (MVP-14). | Minor | Re-trace it to MVP-05/06 and BV-OBJ-05, or lower the priority. |

---

## 5. Security Concerns

Testing constraint: CON-03 forbids penetration and load testing on the shared public demo. Every check below must be **passive and non-destructive**: browser devtools, a single request, no automated attacks.

| ID | Area | Finding | Sev | Recommendation |
|---|---|---|---|---|
| QA-SEC-01 | Auth | Authentication appears to be client-side only. SauceDemo is believed to treat a `session-username` cookie as proof of login, so a hand-made cookie may reach store pages without a password, possibly even as `locked_out_user` **[Validate]**. US-005 only tests "not logged in". | Critical | Add an AC to US-005: a forged or tampered session value doesn't give access. Check it passively with one manual cookie edit. If it is confirmed, log a Critical defect. It is a general-launch blocker alongside R-01. |
| QA-SEC-02 | Auth | Account enumeration and password confirmation through the lockout message (see QA-DEF-02). | Critical | Same as QA-DEF-02. Add it to the BV-001 risk register. |
| QA-SEC-03 | Login | Credentials are shown publicly (R-01, US-007). Already known. | Critical | Keep it as a general-launch blocker. Get PO risk acceptance for MVP 1. |
| QA-SEC-04 | Login | No rate limiting or lockout after failures (QA-MIS-02). | Major | Record it as a requirement plus an accepted, untested gap (CON-03). |
| QA-SEC-05 | Session | No requirements for cookie attributes (Secure, HttpOnly, SameSite), session invalidation on logout or idle timeout (QA-MIS-03). | Major | Add a session-security NFR. Inspect the cookie attributes passively in devtools. |
| QA-SEC-06 | Data | The cart and possibly checkout data are stored in browser storage (localStorage). On a shared device they outlive logout (A-09, R-11). | Major | Add an AC: no customer data is left in browser storage after logout. Check it in devtools. |
| QA-SEC-07 | Input | Checkout fields have no injection or XSS requirement, and no output-encoding requirement. | Major | Add an AC: script and markup input is not executed or rendered. One passive check with a harmless payload such as `<b>x</b>`. |
| QA-SEC-08 | Transport | The business analysis records a TLS certificate error when the AI environment opened the site. No story requires valid HTTPS. | Major | Add an NFR: HTTPS only, with a valid certificate. Re-check from a clean network to tell a SUT problem apart from a local proxy. Never bypass certificate verification in tests. |
| QA-SEC-09 | Payment | US-028 shows a payment identifier but has no masking requirement for real card data in a future release. | Minor | Add a Release 2 AC: only the card brand and last 4 digits are shown. |

---

## 6. Testability Concerns

| ID | Story | Finding | Sev | Recommendation |
|---|---|---|---|---|
| QA-TST-01 | US-022 AC3, US-028 AC2, US-030 AC4, US-033 | These ACs can't be observed through the UI (CON-02). There is no order record, no account data and no email, so "places one order" and "identifies my stored card" cannot be shown to pass or fail. | Critical | Rewrite each one as a UI-observable AC, or tag it **Not testable in MVP 1** with a named residual risk. Don't let a "pass" stand for evidence you can't collect. |
| QA-TST-02 | 12 stories | **[Validate]** placeholders block Ready status for US-002–005, US-008, US-010, US-020, US-023, US-027–030. | Major | Run the exploratory session first (OI-01), then replace every placeholder with the observed value and its date. |
| QA-TST-03 | Catalogue | There is no reference data for names, descriptions, prices and images, so AC1–AC4 of US-008, US-009 and US-026 have no oracle. | Major | Add an appendix with a validated catalogue table, used as the single source for test data. |
| QA-TST-04 | US-027 | "Single, multi and all-6" is a sample. With 6 products there are only 63 non-empty carts, so every combination can be tested. | Major | Require all 63 combinations in automation, with the expected tax and total calculated from the reference table and the QA-AMB-01 rule. For example, all 6 items (believed $129.94) gives tax $10.40 and total $140.34 **[Validate]**. |
| QA-TST-05 | Cross-cutting | No expected results per test user, and `visual_user` needs baseline screenshots that don't exist yet. | Major | Build a story × test-user matrix with expected results. Record visual baselines from `standard_user`. |
| QA-TST-06 | US-019, US-021 | Cart state persists in browser storage, so tests depend on the order they run in. | Major | Make test isolation a Definition of Done item: a new browser context or cleared storage per test. |
| QA-TST-07 | All | No Definition of Ready, Definition of Done, estimates or dependencies, so readiness can't be checked. | Major | Add a DoR (ACs observable, no [Validate], oracle available) and a DoD (tests automated, traced, passed on the agreed browsers). |
| QA-TST-08 | All | The shared public demo may be slow or rate-limited, which makes automation flaky (CON-03). | Major | Use stable `data-test` selectors if the SUT provides them **[Validate]**, wait on explicit conditions, and keep a flakiness log separate from the defect log. |
| QA-TST-09 | US-004 AC2, US-006 AC2 | "No store page is reachable" needs a list of pages to be checked. | Minor | Refer to the page inventory (QA-AMB-11). |

---

## Recommendations (in order)

1. **Resolve the 9 Critical findings before PB-001 is approved:** QA-AMB-01, QA-MIS-01, QA-EDG-01, QA-DEF-01, QA-DEF-02, QA-SEC-01, QA-SEC-02, QA-SEC-03 and QA-TST-01. QA-DEF-02 and QA-SEC-02 are the same issue seen from two angles, so one decision closes both. QA-SEC-01 and the rounding rule in QA-AMB-01 need the exploratory session. The rest need a PO decision.
2. **Run the exploratory session (OI-01)** with this review's **[Validate]** items added to its charter. Update the Assumption Validation Log with the results.
3. **Add reference data:** a page inventory, the catalogue table and a test-account matrix. These remove most of the oracle problems.
4. **Make priority and release criteria agree** (QA-DEF-03, -04, -05), then recalculate the backlog totals.
5. **Add the missing stories and NFRs:** checkout step ordering, session security, input validation, invalid product ID, accessibility, supported browsers.
6. **Add a DoR and DoD** so that "Ready" and "Done" can be checked.
7. **Record new risks in the BV-001 risk register:** client-side auth bypass (QA-SEC-01), account enumeration (QA-SEC-02), duplicate orders (QA-EDG-01), TLS (QA-SEC-08).
8. **Get a human reviewer to confirm this review**, because the AI that wrote PB-001 also wrote this review.

## Conclusion

PB-001 is well structured and traceable, and it is honest about what hasn't been validated. It isn't ready for sprint planning. The main gaps are rounding and oracle definitions for order accuracy, contradictions between the security rules and the stories, ACs that can't be observed through the UI, and missing checkout-flow and session requirements.
