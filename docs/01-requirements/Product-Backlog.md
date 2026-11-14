# Swag Labs Store – Product Backlog (MVP Release 1)

## Document Control

| Field | Value |
|---|---|
| Document ID | PB-001 |
| Version | 0.2 (Validated draft) |
| Status | Validated against the live site on 2026-11-13 ([Exploratory Session 01](Exploratory-Session-01.md)). PO decisions DEC-001 to DEC-006 applied. |
| Artefact Owner | Ayushi Singh – Senior Product Owner |
| Sources | [Business Vision BV-001](Business-Vision.md), [Business Analysis](SauceDemo-Business-Analysis.md), [Assumption Validation Log](Assumption-Validation-Log.md), [Business Vision Review](Business-Vision-Review.md) |
| AI involvement | v0.1 drafted by AI (Kiro) from knowledge of SauceDemo. v0.2 updated with live-site evidence and human decisions. See [AI Usage Log](../00-ai-governance/AI-Usage-Log.md). |
| Change log | v0.2 (correction): items copied from site behaviour marked [PO-pending] and listed in the OB register; the AI's own "not a defect" rulings withdrawn. v0.2: [Validate] markers replaced with observed text; US-034 and US-035 added; US-021 and US-023 raised to High; US-024 approved; DEC-001 applied to US-029; known defects linked to stories. |
| Framing | Swag Labs online retail store (see BV-001 Framing Decision). Special test users are simulated production conditions, not personas. |

## How to Read This Backlog

**Hierarchy:** Epic → Feature → User Story. IDs: `EP-nn`, `FT-nn.n`, `US-nnn`.

**Priority** maps from the BV-001 MoSCoW scope:

| Priority | Meaning | BV-001 equivalent |
|---|---|---|
| High | The purchase journey, access control or order accuracy fails without it | Must |
| Medium | Reduces friction or errors; the journey still completes without it | Should |
| Low | Convenience, or deferred beyond MVP 1 | Could / Won't (this release) |

**Business Value** is scored 1–10 on how much the story protects revenue, customer trust or compliance.

| Score | Meaning |
|---|---|
| 9–10 | No order can be placed, or the customer is charged wrongly, without it |
| 7–8 | Protects accounts, order accuracy or a major risk (R-xx rated High) |
| 4–6 | Reduces abandonment, support contacts or confusion |
| 1–3 | Convenience or brand presence |

**Acceptance criteria** use Given/When/Then.

**Requirements are the test basis.** Where testing found the site different from a story, a defect was logged and the story was **not** changed. Changes to stories come only from Product Owner decisions (DEC-xxx).

**[PO-pending]** marks text in v0.2 that was copied from the live site's behaviour (exact messages, catalogue prices, business rules). Observed behaviour is not a requirement until the PO confirms it. Each item is listed in the "Observed Behaviour Awaiting PO Decision" register at the end. Until it is decided, a test must not use that item as its expected result.

**Known defects** link a story to defects in [Exploratory Session 01](Exploratory-Session-01.md). A story with known defects is correctly specified; the site fails it.

**Primary persona:** BV-USR-01, a returning registered customer, referred to below as "customer".

---

## EP-01 Login (Identity and Access)

**Epic goal:** Only authenticated customers can enter the store, and blocked customers are told why they are refused.
**Objectives:** BV-OBJ-02 | **Capability:** BV-CAP-01 | **Scope:** MVP-01, MVP-02, MVP-03 | **Risks:** R-01, R-10

### FT-01.1 Customer Authentication

#### US-001 Log in with valid credentials
- **As a** returning customer
- **I want** to log in with my username and password
- **So that** I can access the store and buy merchandise

| Priority | Business Value | Traces to |
|---|---|---|
| High | 10 | MVP-01, BV-OBJ-02, SC-01 |

Acceptance criteria:
1. Given I am on the login page, when I enter a valid username and password and select Login, then I am taken to the Products page.
2. Given I have logged in, then the Products page shows the cart icon and the menu.

#### US-002 Refuse invalid credentials
- **As a** customer
- **I want** to be told clearly when my username or password is wrong
- **So that** I can correct my entry, and nobody can enter the store with a wrong password

| Priority | Business Value | Traces to |
|---|---|---|
| High | 8 | MVP-01, BV-OBJ-02, SC-03 |

Acceptance criteria:
1. Given I am on the login page, when I enter a username and password that do not match an account, then I stay on the login page and see "Epic sadface: Username and password do not match any user in this service".
2. The error does not reveal whether the username or the password was wrong.
3. Given an error is shown, when I dismiss it, then the error disappears.
4. **[PO-pending OB-02]** Usernames are case-sensitive and are not trimmed: "Standard_User" and "standard_user " (trailing space) are refused with the AC1 message.

#### US-003 Require username and password
- **As a** customer
- **I want** to be told which field I left empty
- **So that** I can fix it without guessing

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 4 | MVP-01, BV-OBJ-02 |

Acceptance criteria:
1. Given the username is empty, when I select Login, then I see "Epic sadface: Username is required".
2. Given a username is entered and the password is empty, when I select Login, then I see "Epic sadface: Password is required".

#### US-004 Explain a locked account
- **As a** customer whose account is locked
- **I want** to be told my account is locked rather than that my password is wrong
- **So that** I contact support instead of retrying

| Priority | Business Value | Traces to |
|---|---|---|
| High | 7 | MVP-03, BV-USR-02, R-10, SC-03 |

Acceptance criteria:
1. Given I enter the correct credentials for a locked account (simulated by `locked_out_user`), when I select Login, then I stay on the login page and see "Epic sadface: Sorry, this user has been locked out."
2. No store page is reachable for a locked account, including by setting the login cookie by hand (observed: refused).

Note: self-service unlock and password reset are out of scope (OOS-05). Unlocking depends on the support process in A-11.

Security note: with a **wrong** password, a locked account gets the generic US-002 message. The lockout message therefore confirms the password is correct (QA review QA-SEC-02). This conflicts with US-002 AC2 and is open for a PO decision (OI-07).

### FT-01.2 Session Protection

#### US-005 Protect store pages from unauthenticated access
- **As the** Swag Labs business
- **I want** every store page to send unauthenticated visitors to the login page
- **So that** only registered customers can see products and place orders

| Priority | Business Value | Traces to |
|---|---|---|
| High | 9 | MVP-02, BV-OBJ-02, SC-03 |

Acceptance criteria:
1. Given I am not logged in, when I open the URL of the Products, Product detail, Cart, Checkout information, Checkout overview or Checkout complete page directly, then I am shown the login page.
2. The login page shows "Epic sadface: You can only access '/[page]' when you are logged in.", naming the page I tried to open (for example '/cart.html').

Related: US-035 covers access without a real login (faked cookie).

#### US-006 Log out
- **As a** customer
- **I want** to log out from any store page
- **So that** the next person using this device cannot use my account

| Priority | Business Value | Traces to |
|---|---|---|
| High | 8 | MVP-01, BV-OBJ-02, R-11 |

Acceptance criteria:
1. Given I am logged in, when I open the menu and select Logout, then I am taken to the login page.
2. Given I have logged out, when I use the browser Back button or open a store URL, then I am shown the login page, not store content.
3. See US-021 for what happens to the cart after logout.

#### US-007 Do not display credentials on the login page
- **As the** Swag Labs business
- **I want** the login page to show no usernames or passwords
- **So that** nobody can access a customer's account with publicly displayed credentials

| Priority | Business Value | Traces to |
|---|---|---|
| High | 9 | R-01, BV-OBJ-02, BV-STK-07 |

Acceptance criteria:
1. Given I open the login page, then no usernames or passwords are visible anywhere on it.

PO note: the SUT shows every username and the shared password (confirmed 2026-11-13), so this story fails (CON-01, CON-06). Record it as a known High-severity defect. It is accepted only for the controlled MVP 1 release and is a release blocker for a general launch (BV-001 Section 7.4).

#### US-035 Only a real login gives access *(new in v0.2)*
- **As the** Swag Labs business
- **I want** store access to depend on a successful login, not on a value the browser can set
- **So that** nobody can enter a customer's account without the password

| Priority | Business Value | Traces to |
|---|---|---|
| High | 9 | MVP-02, BV-OBJ-02, SC-03, R-01 |

Acceptance criteria:
1. Given I have not logged in, when the login cookie (`session-username`) is set by hand to a valid username, then opening any store page shows the login page.
2. **[PO-pending OB-03]** Given I have logged in, then my session ends 10 minutes after login and I am returned to the login page. (Observed: the cookie expires after 10 minutes. Whether activity extends it is not yet confirmed.)
3. The login cookie is marked HttpOnly and Secure. (Observed: neither is set.)

**Known defects:** DEF-007 (Critical): AC1 fails for `standard_user`; the shop opens with no password. AC3 also fails.

Testing note: check AC1 with one hand-set cookie only. No automated attacks on the shared demo (CON-03).

---

## EP-02 Product Catalogue

**Epic goal:** A customer can quickly find, understand and compare the six products on offer.
**Objectives:** BV-OBJ-01 | **Capabilities:** BV-CAP-02, BV-CAP-03 | **Scope:** MVP-04, MVP-10, MVP-13 | **Risks:** R-06, R-12

### FT-02.1 Product Listing

#### US-008 View the product list
- **As a** customer
- **I want** to see all available products with their name, description, price and image
- **So that** I can choose what to buy

| Priority | Business Value | Traces to |
|---|---|---|
| High | 9 | MVP-04, BV-OBJ-01, A-06, A-07 |

Acceptance criteria:
1. Given I have logged in, then the Products page lists exactly the products in the reference catalogue below.
2. Each product shows a name, a short description, a price and an image.
3. Each product shows its own image, not a placeholder or another product's image.
4. Each price matches the reference catalogue, in US dollars with 2 decimal places (for example "$29.99").
5. Each product has an Add to cart control (see US-012).

**[PO-pending OB-04]** Reference catalogue (copied from the live site on 2026-11-13, not yet confirmed by the PO as the official price list; detail page `inventory-item.html?id=N`):

| ID | Product | Price |
|---|---|---|
| 4 | Sauce Labs Backpack | $29.99 |
| 0 | Sauce Labs Bike Light | $9.99 |
| 1 | Sauce Labs Bolt T-Shirt | $15.99 |
| 5 | Sauce Labs Fleece Jacket | $49.99 |
| 2 | Sauce Labs Onesie | $7.99 |
| 3 | Test.allTheThings() T-Shirt (Red) | $15.99 |

**Known defects:** DEF-009 (`problem_user`: all images "not found"), DEF-016 (`visual_user`: every price wrong, e.g. Backpack $36.43, Onesie "$47.1"), DEF-017 (`visual_user`: Backpack image broken).

### FT-02.2 Product Details

#### US-009 View a product's details
- **As a** customer
- **I want** to open a page for a single product
- **So that** I can read about it before deciding to buy

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 5 | MVP-10 |

Acceptance criteria:
1. Given I am on the Products page, when I select a product's name or image, then its detail page opens.
2. The detail page shows the same name, description, price and image as the listing for that product.
3. Given I am on a detail page, when I select Back to products, then I return to the Products page.
4. Given I open a detail page for a product that doesn't exist (for example `?id=99`), then I see a clear "not found" message with no price, and can return to Products.

Observed for AC4: "ITEM NOT FOUND" with joke text and the price "$√-1". **[PO-pending OB-07]** AC4 itself was added by the AI in v0.2 and needs PO approval. If approved, the price shown fails AC4 and should be raised as a defect. The AI should not have decided "not yet raised as a defect".

### FT-02.3 Sorting

#### US-010 Sort products by name
- **As a** customer
- **I want** to sort products alphabetically, A to Z or Z to A
- **So that** I can find a product I already know by name

| Priority | Business Value | Traces to |
|---|---|---|
| Low | 3 | MVP-13 |

Acceptance criteria:
1. Given I open the Products page, then products are sorted by Name (A to Z) by default.
2. When I select Name (Z to A), then products are listed in reverse alphabetical order.
3. When I select Name (A to Z), then products are listed in alphabetical order.

Sort options observed: Name (A to Z), Name (Z to A), Price (low to high), Price (high to low).

**Known defects:** DEF-012 (`problem_user`: sort does nothing; `error_user`: "Sorting is broken!" popup).

#### US-011 Sort products by price
- **As a** customer
- **I want** to sort products by price, low to high or high to low
- **So that** I can find products that fit my budget

| Priority | Business Value | Traces to |
|---|---|---|
| Low | 4 | MVP-13 |

Acceptance criteria:
1. When I select Price (low to high), then each product's price is less than or equal to the next one's.
2. When I select Price (high to low), then each product's price is greater than or equal to the next one's.
3. The chosen sort option stays selected while I remain on the Products page.
4. **[PO-pending OB-05]** Products with the same price are ordered by name, A to Z. (Observed: Bolt T-Shirt and Test.allTheThings() T-Shirt both cost $15.99, and Bolt is listed first in both directions.)

**[PO-pending OB-06]** Observed: the sort resets to Name (A to Z) after opening a product and returning. The stories don't say whether the sort should be kept. The PO decides: add a requirement (the site then has a defect) or accept the behaviour. The AI wrongly decided "it isn't a defect" in the first v0.2 edit.

**Known defects:** DEF-012.

PO note: sorting does not trace to a business objective (BV-001 Appendix B). It is a candidate for removal from MVP 1 if capacity is short.

---

## EP-03 Shopping Cart

**Epic goal:** The cart always reflects exactly what the customer chose, wherever they added or removed it.
**Objectives:** BV-OBJ-05 | **Capabilities:** BV-CAP-04, BV-CAP-06 | **Scope:** MVP-05, MVP-06, MVP-14 | **Risks:** R-11

### FT-03.1 Add and Remove Products

#### US-012 Add a product from the product list
- **As a** customer
- **I want** to add a product to my cart from the Products page
- **So that** I can buy it without opening its detail page

| Priority | Business Value | Traces to |
|---|---|---|
| High | 10 | MVP-05, BV-OBJ-01, BV-OBJ-05 |

Acceptance criteria:
1. Given a product is not in my cart, when I select Add to cart on it, then it is added to my cart and its control changes to Remove.
2. The cart badge count increases by 1 (see US-016).
3. AC1 and AC2 hold for every product in the reference catalogue (US-008).

**Known defects:** DEF-010 (`problem_user`, `error_user`: Bolt T-Shirt, Fleece Jacket and Test.allTheThings() T-Shirt can't be added).

#### US-013 Add a product from its detail page
- **As a** customer
- **I want** to add a product to my cart from its detail page
- **So that** I can buy it straight after reading about it

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 5 | MVP-05, MVP-10 |

Acceptance criteria:
1. Given I am on a product's detail page and it is not in my cart, when I select Add to cart, then it is added and the control changes to Remove.
2. When I return to the Products page, that product shows Remove.

#### US-014 Remove a product from the product list or detail page
- **As a** customer
- **I want** to remove a product I added by mistake without going to the cart
- **So that** I don't order something I don't want

| Priority | Business Value | Traces to |
|---|---|---|
| High | 7 | MVP-05, BV-OBJ-05 |

Acceptance criteria:
1. Given a product is in my cart, when I select Remove on the Products page or its detail page, then it is removed from my cart and the control changes back to Add to cart.
2. The cart badge count decreases by 1, and the badge disappears when the cart is empty.

**Known defects:** DEF-011 (`problem_user`, `error_user`: Remove on the product list doesn't remove the item).

### FT-03.2 Cart Review

#### US-015 View my cart
- **As a** customer
- **I want** to see everything in my cart before checkout
- **So that** I can confirm I am buying the right items

| Priority | Business Value | Traces to |
|---|---|---|
| High | 9 | MVP-06, BV-OBJ-01 |

Acceptance criteria:
1. Given I have items in my cart, when I select the cart icon from any store page, then the Cart page lists each item with its quantity, name, description and price.
2. Prices on the Cart page match the Products page.
3. Each item appears once with quantity 1. Changing quantity is out of scope (OOS-06).
4. From the Cart page I can select Checkout (EP-04) or Continue Shopping (US-018).

#### US-016 See an accurate cart count
- **As a** customer
- **I want** the cart icon to show how many items are in my cart
- **So that** I know what I've chosen without opening the cart

| Priority | Business Value | Traces to |
|---|---|---|
| High | 7 | MVP-05, BV-OBJ-05, SC-05 |

Acceptance criteria:
1. After every add or remove, from any location (list, detail page, cart), the badge number equals the number of items on the Cart page.
2. When the cart is empty, no badge number is shown.
3. The badge count is the same on every store page.

#### US-017 Remove a product from the cart page
- **As a** customer
- **I want** to remove an item while reviewing my cart
- **So that** I only check out what I actually want

| Priority | Business Value | Traces to |
|---|---|---|
| High | 7 | MVP-05, MVP-06, BV-OBJ-05 |

Acceptance criteria:
1. Given I am on the Cart page, when I select Remove on an item, then it disappears from the cart and the badge count decreases by 1.
2. When I return to the Products page, that product shows Add to cart.

#### US-018 Continue shopping from the cart
- **As a** customer
- **I want** to go back to the products from my cart
- **So that** I can add more items before I check out

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 4 | MVP-06 |

Acceptance criteria:
1. Given I am on the Cart page, when I select Continue Shopping, then I return to the Products page and my cart contents are unchanged.

### FT-03.3 Cart State

#### US-019 Keep my cart while I browse
- **As a** customer
- **I want** my cart to stay the same as I move between pages or refresh the browser
- **So that** I don't have to add items again

| Priority | Business Value | Traces to |
|---|---|---|
| High | 7 | MVP-05, BV-CAP-06 |

Acceptance criteria:
1. Given I have items in my cart, when I move between store pages or refresh the page, then my cart contents and badge count are unchanged.

#### US-020 Reset the store to its starting state
- **As a** customer
- **I want** to clear my cart and selections in one step
- **So that** I can start my shopping again

| Priority | Business Value | Traces to |
|---|---|---|
| Low | 2 | MVP-14 |

Acceptance criteria:
1. Given I have items in my cart, when I open the menu and select Reset App State, then the cart is empty and the badge disappears.
2. Every product on the current page shows Add to cart, without reloading the page.

**Known defects:** DEF-005 (buttons still say "Remove" until the page is reloaded).

#### US-021 Don't expose my cart to the next user
- **As a** customer who shares a device
- **I want** my cart to be private to my account
- **So that** the next person to log in cannot see or order my items

| Priority | Business Value | Traces to |
|---|---|---|
| High (raised by DEC-004) | 7 | R-11, A-09, BV-OBJ-05 |

Acceptance criteria:
1. Given I logged out with items in my cart, when a different customer logs in on the same device, then their cart is empty.
2. Given I logged out with items in my cart, when I log back in on the same device, then my cart is empty (DEC-003).
3. After logout, no cart contents remain in browser storage (`cart-contents`).

**Known defects:** DEF-006 (Major): the cart stays in browser storage after logout, and the next user (`problem_user`) saw `standard_user`'s Backpack and Bike Light. A-09 confirmed.

PO note: priority is High although the story is outside the original Must scope, because it is a privacy risk (DEC-004).

---

## EP-04 Checkout

**Epic goal:** A customer gives the minimum information needed and sees exactly what they will pay before committing.
**Objectives:** BV-OBJ-01, BV-OBJ-03, BV-OBJ-04 | **Capability:** BV-CAP-05 | **Scope:** MVP-07, MVP-08, MVP-11, MVP-12 | **Risks:** R-02, R-03, R-05, R-08

### FT-04.1 Customer Information

#### US-022 Enter my checkout details
- **As a** customer
- **I want** to enter my first name, last name and postal code
- **So that** my order is matched to me and my stored delivery address

| Priority | Business Value | Traces to |
|---|---|---|
| High | 10 | MVP-07, BV-OBJ-01, BV-OBJ-04, A-03 |

Acceptance criteria:
1. Given I have items in my cart, when I select Checkout, then the Checkout information page asks for first name, last name and postal code.
2. When I complete all 3 fields and select Continue, then the Checkout overview page opens.
3. The details I entered are not changed or lost when I continue.
4. Each field accepts the text I type into that field, and only that field.
5. A field containing only spaces is treated as empty and shows the US-023 message.

**Known defects:** DEF-002 (spaces-only details accepted), DEF-013 (`problem_user`: last name is typed into the first name field, so checkout can't continue).

PO note: postal code alone cannot deliver goods (R-03). MVP 1 relies on A-03, that the full address is held on the account.

#### US-023 Be told which checkout field is missing
- **As a** customer
- **I want** a clear message when a required field is empty
- **So that** I can fix it and carry on

| Priority | Business Value | Traces to |
|---|---|---|
| High (raised by DEC-005) | 6 | MVP-11, BV-OBJ-04, SC-04 |

Acceptance criteria:
1. Given first name is empty, when I select Continue, then I stay on the page and see "Error: First Name is required".
2. Given first name is complete and last name is empty, then I see "Error: Last Name is required".
3. Given first and last name are complete and postal code is empty, then I see "Error: Postal Code is required".
4. Text I already entered in other fields is kept.

**Known defects:** DEF-014 (`error_user`: last name can't be entered, but Continue is accepted without it).

PO note: raised to High to match release gate SC-04 (DEC-005).

#### US-024 Prevent checkout with an empty cart
- **As the** Swag Labs business
- **I want** checkout to be unavailable when the cart is empty
- **So that** no empty orders are created for support and fulfilment to handle

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 5 | BV-OBJ-01, BV-STK-09 |

Acceptance criteria:
1. Given my cart is empty, then I cannot get past the Cart page into checkout, whether by the Checkout button or by opening a checkout page's address.

**Known defects:** DEF-001 (Major): an order completes with an empty cart, showing Total $0.00.

PO note: approved on 2026-11-13 (DEC-002). This is new scope beyond BV-001; the vision's MVP list needs updating (OI-06).

#### US-025 Cancel from the checkout information page
- **As a** customer
- **I want** to leave checkout before I commit
- **So that** I can change my cart

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 4 | MVP-12, BV-OBJ-04 |

Acceptance criteria:
1. Given I am on the Checkout information page, when I select Cancel, then I return to the Cart page and my cart is unchanged. (Observed: works.)

### FT-04.2 Order Overview

#### US-026 Review my order before buying
- **As a** customer
- **I want** to see the items I am about to buy on one page
- **So that** I can check my order before committing

| Priority | Business Value | Traces to |
|---|---|---|
| High | 9 | MVP-08, BV-OBJ-01 |

Acceptance criteria:
1. Given I have submitted valid checkout details, then the Checkout overview lists every cart item with quantity, name, description and price.
2. The items and prices match the Cart page exactly.

#### US-027 See an accurate order total
- **As a** customer
- **I want** the subtotal, tax and total to be correct
- **So that** I am charged the right amount

| Priority | Business Value | Traces to |
|---|---|---|
| High | 10 | MVP-08, BV-OBJ-03, SC-02, A-05, R-08 |

Acceptance criteria:
1. "Item total" equals the sum of item prices in the order, using the prices shown to the customer.
2. "Tax" equals the item total × 8%, rounded to 2 decimal places (A-05 confirmed).
3. "Total" equals item total + tax.
4. Item total, tax and total are each shown in dollars with exactly 2 decimal places.
5. Criteria 1–4 hold for every possible cart (63 combinations of the 6 products).

Observed example: all 6 items gives Item total $129.94, Tax $10.40, Total $140.34.

Open point: the rounding method at exactly half a cent hasn't been observed (QA-AMB-01).

**Known defects:** DEF-008 (Major, every user): 5 items show "Item total: $119.94999999999999"; tax and total are correct. DEF-016 (Critical, `visual_user`): the prices charged differ from the prices shown.

#### US-028 See how I will pay and how my order will be delivered
- **As a** customer
- **I want** to see the payment method and shipping option for my order
- **So that** I know how I will be charged and how it will arrive

| Priority | Business Value | Traces to |
|---|---|---|
| High | 7 | MVP-08, A-02, A-03, A-04, R-02 |

Acceptance criteria:
1. The Checkout overview shows payment information and shipping information before I finish. (Observed: "SauceCard #31337" and "Free Pony Express Delivery!".)
2. Under A-02, the payment information identifies the card stored on my account.
3. Under A-04, shipping is a single free option and adds nothing to the total.

PO note: no real payment is captured (R-02). Go for MVP 1 requires A-02 to be Confirmed or accepted as residual risk (SC-08).

#### US-034 Checkout steps happen in order *(new in v0.2)*
- **As the** Swag Labs business
- **I want** each checkout page to be reachable only after the step before it is complete
- **So that** a customer is never told an order was placed when it wasn't

| Priority | Business Value | Traces to |
|---|---|---|
| High | 9 | MVP-07, MVP-08, MVP-09, BV-OBJ-01 |

Acceptance criteria:
1. Given I haven't submitted valid checkout details, when I open the Checkout overview address directly, then I am sent to the Checkout information page.
2. Given I haven't selected Finish, when I open the Checkout complete address directly, then no confirmation is shown and my cart is unchanged.
3. The "Thank you for your order!" message appears only after I select Finish on the overview.

**Known defects:** DEF-004 (Critical): opening the Checkout complete address directly shows "Thank you for your order!" while the item is still in the cart. The overview can also be opened directly.

#### US-029 Cancel from the order overview
- **As a** customer
- **I want** to cancel at the overview without placing the order
- **So that** I'm not committed if something looks wrong

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 4 | MVP-12, BV-OBJ-04 |

Acceptance criteria:
1. Given I am on the Checkout overview, when I select Cancel, then no order is placed and I return to the **Cart page** with my cart unchanged (DEC-001).

**Known defects:** DEF-003 (Minor): Cancel goes to the Products page. The cart is kept.

---

## EP-05 Order Confirmation

**Epic goal:** A customer knows their order went through, and the store is ready for their next purchase.
**Objectives:** BV-OBJ-01 | **Capability:** BV-CAP-05 | **Scope:** MVP-09, OOS-04 | **Risks:** R-04, R-05

### FT-05.1 Place Order

#### US-030 Place my order and see confirmation
- **As a** customer
- **I want** to confirm my order and see that it was received
- **So that** I know my purchase is complete

| Priority | Business Value | Traces to |
|---|---|---|
| High | 10 | MVP-09, BV-OBJ-01, SC-01 |

Acceptance criteria:
1. Given I am on the Checkout overview, when I select Finish, then the Checkout complete page opens.
2. The page shows "Thank you for your order!"
3. The page shows "Your order has been dispatched, and will arrive just as fast as the pony can get there!"
4. Selecting Finish once places one order. (Not observable in the UI: there is no order record. See QA-TST-01.)

Observed: browser Back after Finish returns to the overview with an empty cart. Behaviour on a second Finish isn't checked yet.

**Known defects:** DEF-015 (Critical, `error_user`): Finish does nothing, so the order can't be placed.

### FT-05.2 After the Order

#### US-031 Empty my cart after ordering
- **As a** customer
- **I want** my cart to be empty once my order is placed
- **So that** I don't order the same items twice by mistake

| Priority | Business Value | Traces to |
|---|---|---|
| High | 8 | MVP-09, BV-OBJ-05 |

Acceptance criteria:
1. Given my order is confirmed, then the cart badge is not shown and the Cart page is empty.
2. Every product shows Add to cart.

#### US-032 Return to the store after ordering
- **As a** customer
- **I want** to go back to the products from the confirmation page
- **So that** I can keep shopping

| Priority | Business Value | Traces to |
|---|---|---|
| Medium | 3 | MVP-09 |

Acceptance criteria:
1. Given I am on the Checkout complete page, when I select Back Home, then the Products page opens with an empty cart.

### FT-05.3 Order Traceability (Release 2 candidate)

#### US-033 Receive an order reference and confirmation email
- **As a** customer
- **I want** an order number on screen and a confirmation email
- **So that** I have proof of purchase and can quote it if I contact support

| Priority | Business Value | Traces to |
|---|---|---|
| Low (MVP 1) | 8 | OOS-04, R-04, BV-STK-08 |

Acceptance criteria:
1. The Checkout complete page shows a unique order number.
2. A confirmation email with the order number, items and total is sent to the email address on my account.

PO note: high value but deliberately Low priority for MVP 1, because the SUT cannot be changed (CON-01) and BV-001 accepts R-04 for the controlled release. It becomes High for Release 2 and is a precondition for a general launch.

---

## Cross-Cutting: Degraded Conditions

These apply to every High story rather than forming separate stories. They support BV-OBJ-06 and SC-06.

| Condition | Simulated by | Expectation on High stories | Observed 2026-11-13 | Can buy? | Defects | Risk |
|---|---|---|---|---|---|---|
| Faulty release | `problem_user` | Images, sorting, cart and form fields behave as in the acceptance criteria above | All images "not found"; sort does nothing; 3 products can't be added; Remove fails; last name typed into the first name field | No | DEF-009, -010, -011, -012, -013 | R-06 |
| Degraded performance | `performance_glitch_user` | Journey completes; delay recorded functionally only (CON-03) | Login 5.1 s (others 0.1–0.2 s, measured once); journey completes | Yes | DEF-018 | R-09 |
| Action errors | `error_user` | No action in cart or checkout fails silently or blocks the purchase | "Sorting is broken!" popup; 3 products can't be added; Remove fails; last name can't be entered but is not required; Finish does nothing | No | DEF-010, -011, -012, -014, -015 | R-05 |
| Visual regression | `visual_user` | All controls visible, correctly placed and labelled; prices match the catalogue | Every price shown is wrong but real prices are charged; Backpack image broken; cart icon moved | Yes (at different prices than shown) | DEF-016, -017 | R-12 |

A-10 is confirmed. Each journey was run once, so repeat runs are needed before treating timing as stable.

---

## Backlog Summary

| Epic | Feature | Story | Title | Priority | Value |
|---|---|---|---|---|---|
| EP-01 | FT-01.1 | US-001 | Log in with valid credentials | High | 10 |
| EP-01 | FT-01.1 | US-002 | Refuse invalid credentials | High | 8 |
| EP-01 | FT-01.1 | US-003 | Require username and password | Medium | 4 |
| EP-01 | FT-01.1 | US-004 | Explain a locked account | High | 7 |
| EP-01 | FT-01.2 | US-005 | Protect store pages | High | 9 |
| EP-01 | FT-01.2 | US-006 | Log out | High | 8 |
| EP-01 | FT-01.2 | US-007 | Do not display credentials | High | 9 |
| EP-01 | FT-01.2 | US-035 | Only a real login gives access *(new)* | High | 9 |
| EP-02 | FT-02.1 | US-008 | View the product list | High | 9 |
| EP-02 | FT-02.2 | US-009 | View a product's details | Medium | 5 |
| EP-02 | FT-02.3 | US-010 | Sort by name | Low | 3 |
| EP-02 | FT-02.3 | US-011 | Sort by price | Low | 4 |
| EP-03 | FT-03.1 | US-012 | Add from product list | High | 10 |
| EP-03 | FT-03.1 | US-013 | Add from detail page | Medium | 5 |
| EP-03 | FT-03.1 | US-014 | Remove from list or detail page | High | 7 |
| EP-03 | FT-03.2 | US-015 | View my cart | High | 9 |
| EP-03 | FT-03.2 | US-016 | Accurate cart count | High | 7 |
| EP-03 | FT-03.2 | US-017 | Remove from cart page | High | 7 |
| EP-03 | FT-03.2 | US-018 | Continue shopping | Medium | 4 |
| EP-03 | FT-03.3 | US-019 | Keep cart while browsing | High | 7 |
| EP-03 | FT-03.3 | US-020 | Reset App State | Low | 2 |
| EP-03 | FT-03.3 | US-021 | Cart private to the next user | High *(was Medium)* | 7 |
| EP-04 | FT-04.1 | US-022 | Enter checkout details | High | 10 |
| EP-04 | FT-04.1 | US-023 | Missing-field messages | High *(was Medium)* | 6 |
| EP-04 | FT-04.1 | US-024 | Prevent empty-cart checkout | Medium | 5 |
| EP-04 | FT-04.1 | US-025 | Cancel from information page | Medium | 4 |
| EP-04 | FT-04.2 | US-026 | Review order | High | 9 |
| EP-04 | FT-04.2 | US-027 | Accurate order total | High | 10 |
| EP-04 | FT-04.2 | US-028 | Payment and shipping shown | High | 7 |
| EP-04 | FT-04.2 | US-034 | Checkout steps in order *(new)* | High | 9 |
| EP-04 | FT-04.2 | US-029 | Cancel from overview | Medium | 4 |
| EP-05 | FT-05.1 | US-030 | Place order and see confirmation | High | 10 |
| EP-05 | FT-05.2 | US-031 | Empty cart after ordering | High | 8 |
| EP-05 | FT-05.2 | US-032 | Back Home | Medium | 3 |
| EP-05 | FT-05.3 | US-033 | Order reference and email | Low (MVP 1) | 8 |

Totals: 35 stories. High 23, Medium 8, Low 4.

## Open Items for the Product Owner

| ID | Item | Status |
|---|---|---|
| OI-01 | Validate UI text and the tax rate (A-05) | **Closed** 2026-11-13: all observed; 8% confirmed |
| OI-02 | Validate cart after logout (A-09) and decide re-login behaviour | **Closed**: confirmed (DEF-006); start empty (DEC-003) |
| OI-03 | Validate `error_user` and `visual_user` behaviour (A-10) | **Closed**: see Cross-Cutting table |
| OI-04 | Approve or reject US-024 | **Closed**: approved (DEC-002) |
| OI-05 | Confirm A-02 and A-03 or accept them as residual risk before Go (SC-08) | Open. Needed by Day 7 (release decision). |
| OI-06 | Update BV-001 MVP scope to include US-024, US-034 and US-035 | Open |
| OI-07 | Decide the US-002 vs US-004 conflict: the lockout message confirms the password is correct | Open |
| OI-08 | A human confirms the Critical findings of the QA review (QAR-001), which was an AI self-review | Open |
| OI-09 | Decide OB-01 to OB-07 (observed behaviour copied into the stories) | Open. Needed before test design on Day 3. |

## Observed Behaviour Awaiting PO Decision

In the v0.2 edit, the AI copied these items from the live site's behaviour into the stories. Observed behaviour is not a requirement. For each one, the PO decides: **Accept** (it becomes a requirement) or **Reject** (write the intended rule; the site then has a defect).

| ID | Story | Copied from the site | Decision needed | Decision |
|---|---|---|---|---|
| OB-01 | US-002, US-003, US-004, US-005, US-023, US-028, US-030 | Exact screen messages, e.g. "Epic sadface: …", "Error: First Name is required", "Thank you for your order!" | Is this the approved wording? Normally confirmed by the PO or design. | Pending |
| OB-02 | US-002 AC4 | Usernames are case-sensitive and not trimmed | Is that the intended login rule? | Pending |
| OB-03 | US-035 AC2 | Session ends 10 minutes after login | What should the session timeout be? | Pending |
| OB-04 | US-008 | Catalogue product names and prices | Is this the official price list? | Pending |
| OB-05 | US-011 AC4 | Equal prices are ordered by name, A to Z | Is that the intended tie-break? | Pending |
| OB-06 | US-011 | Sort resets after opening a product (the AI wrongly ruled "not a defect") | Should the sort choice be kept? | Pending |
| OB-07 | US-009 AC4 | A not-found page for invalid products, with no price (AC added by the AI; the AI wrongly ruled "not yet raised as a defect") | Approve AC4? If yes, "$√-1" becomes a defect. | Pending |

Items that are **not** in this register because they came from the business vision or a recorded PO decision, not from the site: the 8% tax (A-05), free shipping (A-04), Cancel destinations (DEC-001), re-login with an empty cart (DEC-003).
