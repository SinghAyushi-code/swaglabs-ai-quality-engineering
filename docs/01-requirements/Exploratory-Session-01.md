# Exploratory Session 01: Validating the Requirements

| Field | Value |
|---|---|
| Date | 2026-11-13 |
| Site | https://www.saucedemo.com (live) |
| Tester | Ayushi Singh, with Kiro driving the browser through Playwright MCP |
| Goal | Replace guesses in the Product Backlog (PB-001) with observed facts |
| Approach | Read-only, customer-like actions. No load or attack testing (CON-03). |
| Status | Complete. 18 defects found. Results applied to Product Backlog v0.2 and the Assumption Validation Log. |

## Group 0: Login and catalogue (checked first)

| Check | Observed | Result |
|---|---|---|
| Empty username | "Epic sadface: Username is required" | Matches backlog |
| Empty password | "Epic sadface: Password is required" | Matches backlog |
| Wrong password or unknown user | "Epic sadface: Username and password do not match any user in this service" | Matches backlog |
| `locked_out_user`, correct password | "Epic sadface: Sorry, this user has been locked out." | Matches backlog |
| `locked_out_user`, wrong password | Generic "do not match" message | The lockout message only shows with the correct password, so it confirms the password is right (security finding) |
| Username in a different case, or with a trailing space | Refused | Usernames are case-sensitive and not trimmed |
| Store pages opened without logging in (all 6) | Sent to login with "You can only access '/page' when you are logged in." | Matches backlog |
| Login page | Shows every username and the shared password | US-007 fails, as expected |
| Products | 6 items: Backpack $29.99, Bike Light $9.99, Bolt T-Shirt $15.99, Fleece Jacket $49.99, Onesie $7.99, Test.allTheThings() T-Shirt (Red) $15.99 | A-06 and A-07 confirmed |
| Default sort | Name (A to Z) | Matches backlog |
| Equal prices | Bolt T-Shirt and Test.allTheThings() both $15.99; Bolt listed first either way | Tie-break is needed in requirements |
| Sort after opening a product and returning | Resets to A to Z | Sort choice is not kept |
| Invalid product (`?id=99`) | "ITEM NOT FOUND", joke text, price "$√-1" | Defect (minor) |
| Login cookie | `session-username`, not HttpOnly or Secure, expires after 10 minutes | Security finding; 10-minute session |

## Group 1: Cart and checkout

| # | Check | Observed | Result |
|---|---|---|---|
| 1 | Cart count | 1 → 2 → 1 → 2 across list and detail page; survives refresh; stored in browser as `cart-contents` | Works |
| 2 | Empty-cart checkout | Reaches "Thank you for your order!" with Total $0.00 | **DEF-001** |
| 3 | Missing-field messages | "Error: First Name is required", "Error: Last Name is required", "Error: Postal Code is required"; typed text kept | Works |
| 3b | Spaces-only details | Accepted; goes to overview | **DEF-002** |
| 4 | Cancel destinations | Details page → Cart. Overview page → **Products**. Cart kept. | **DEF-003** (decision below) |
| 5 | Payment and delivery text | "SauceCard #31337", "Free Pony Express Delivery!" | As expected |
| 6 | Tax and totals (6 carts, $7.99 to $129.94) | Tax = 8% rounded to 2 dp; total correct every time. All 6 items: $129.94 + $10.40 = $140.34 | Works. A-05 confirmed. |
| 7 | Confirmation | "Thank you for your order!" / "Your order has been dispatched, and will arrive just as fast as the pony can get there!" Cart empties. | Works |
| 8 | Skipping steps by URL | Opening the confirmation page directly shows "Thank you for your order!" but the item stays in the cart | **DEF-004** |
| 9 | Reset App State | Cart count clears, but buttons still say "Remove" until reload | **DEF-005** |

## Group 2: Privacy and security

| # | Check | Observed | Result |
|---|---|---|---|
| 10a | Logout | Login cookie removed; browser Back goes to login with "You can only access '/inventory.html' when you are logged in." | Works (US-006) |
| 10b | Cart after logout | Cart stays in browser storage (`cart-contents: [4,0]`). `problem_user` logging in next on the same browser sees `standard_user`'s Backpack and Bike Light, with the count showing 2. | **DEF-006**. A-09 confirmed (cart persists). |
| 11a | Hand-set login cookie `session-username=standard_user`, no password entered | Products page opens with all 6 products | **DEF-007** |
| 11b | Same, as `locked_out_user` | Refused, sent to login | A locked account can't be entered this way |

## Group 3: Special test users

Each user ran the same journey in a fresh browser: log in, view products, sort by price (high to low), add all 6 items, remove the Bike Light, check out as Jane / Doe / 12345, and finish. `standard_user` is the baseline. Each journey was run once.

| Check | standard_user (baseline) | problem_user | error_user | visual_user | performance_glitch_user |
|---|---|---|---|---|---|
| Login time | 0.2 s | 0.1 s | 0.1 s | 0.1 s | **5.1 s** |
| Product images | 6 correct | **All 6 show the same "not found" image** | Correct | **Backpack shows "not found" image** | Correct |
| Prices on product list | Correct | Correct | Correct | **All 6 wrong**, e.g. Backpack $36.43, Onesie $47.1 | Correct |
| Cart icon position | 1220,10 | Same | Same | **Moved to 1034,39** | Same |
| Sort by price (high to low) | Works | **Does nothing** | **Popup: "Sorting is broken! This error has been reported to Backtrace."** | Works | Works |
| Add all 6 | 6 added | **Only 3 added**: Bolt T-Shirt, Fleece Jacket and Test.allTheThings() fail | **Same 3 fail** | 6 added | 6 added |
| Remove Bike Light | Works | **Doesn't remove** | **Doesn't remove** | Works | Works |
| Checkout details | Accepted | **Last name typed into the first name field**; blocked with "Error: Last Name is required" | **Last name can't be typed, but Continue is accepted without it** | Accepted | Accepted |
| Overview totals | **Item total: $119.94999999999999**, Tax $9.60, Total $129.55 | Not reached | $47.97 / $3.84 / $51.81 (correct) | Same as baseline, charged at **real** prices, not the prices shown | Same as baseline |
| Finish | Order confirmed | Not reached | **Finish does nothing** | Order confirmed | Order confirmed |
| Can the customer buy? | Yes | **No** | **No** | Yes, but at different prices than shown | Yes, after a slow login |

## Defects Found

Severity uses the "we own this shop" framing.

| ID | Defect | Severity | Story |
|---|---|---|---|
| DEF-001 | An order can be completed with an empty cart ($0.00) | Major | US-024 |
| DEF-002 | Checkout accepts spaces as first name, last name and postal code | Major | US-022, US-023 |
| DEF-003 | Cancel on the overview goes to Products, while Cancel on the details page goes to the Cart | Minor | US-029 |
| DEF-004 | The confirmation page can be opened directly, saying an order was placed when it wasn't | Critical | New story needed (checkout steps in order) |
| DEF-005 | After Reset App State, product buttons still say "Remove" until the page is reloaded | Minor | US-020 |
| DEF-006 | After logout, the next user on the same browser gets the previous user's cart | Major | US-021 |
| DEF-007 | The shop can be entered without a password by setting the login cookie by hand | Critical | US-005 (new AC needed) |
| DEF-008 | Item total shows a floating-point error ("$119.94999999999999") for some carts. Seen for every user that reached the overview. Tax and total are correct. | Major | US-027 |
| DEF-009 | `problem_user`: every product shows the same "not found" image | Major | US-008 |
| DEF-010 | `problem_user`, `error_user`: Bolt T-Shirt, Fleece Jacket and Test.allTheThings() T-Shirt can't be added to the cart | Critical | US-012 |
| DEF-011 | `problem_user`, `error_user`: Remove on the product list doesn't remove the item | Major | US-014 |
| DEF-012 | `problem_user`: sorting does nothing. `error_user`: sorting shows "Sorting is broken!" | Minor | US-010, US-011 |
| DEF-013 | `problem_user`: last name is typed into the first name field, so checkout can't continue | Critical | US-022 |
| DEF-014 | `error_user`: last name can't be entered, but checkout continues without it (validation bypassed) | Major | US-023 |
| DEF-015 | `error_user`: Finish does nothing, so the order can't be placed | Critical | US-030 |
| DEF-016 | `visual_user`: prices shown on the product list differ from the prices charged at checkout | Critical | US-008, US-027 |
| DEF-017 | `visual_user`: Backpack image broken; cart icon moved out of position | Minor | US-008, cross-cutting |
| DEF-018 | `performance_glitch_user`: login takes 5.1 s against 0.1–0.2 s for other users (measured once) | Minor | Cross-cutting (functional check only, CON-03) |

## Decisions

| ID | Date | Decision | Made by | Effect |
|---|---|---|---|---|
| DEC-001 | 2026-11-13 | Cancel on the overview should return to the Cart, like Cancel on the details page. The current behaviour is a defect (DEF-003), not accepted behaviour. | Ayushi Singh (PO role) | US-029 AC1 changed to "return to the Cart page" |
| DEC-002 | 2026-11-13 | Approve US-024: checkout must be blocked when the cart is empty | Ayushi Singh (PO role) | US-024 approved; DEF-001 confirmed as a defect |
| DEC-003 | 2026-11-13 | On logging back in, the customer starts with an empty cart | Ayushi Singh (PO role) | US-021 AC2 resolved |
| DEC-004 | 2026-11-13 | Raise US-021 (cart privacy) from Medium to High | Ayushi Singh (PO role) | Priority matches the privacy risk |
| DEC-005 | 2026-11-13 | Raise US-023 (checkout field messages) from Medium to High | Ayushi Singh (PO role) | Priority matches release gate SC-04 |
| DEC-006 | 2026-11-13 | Accept the 18 defect severities as proposed | Ayushi Singh (QA Lead role) | Severities above are final for v0.2 |
