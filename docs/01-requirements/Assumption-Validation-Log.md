# Assumption Validation Log

**Owner:** Ayushi Singh | **Last updated:** 2026-11-13 | **Evidence:** [Exploratory Session 01](Exploratory-Session-01.md)

| ID | Assumption | Validation Method | Status | Notes |
|----|------------|------------------|--------|--------|
| A-02 | Payment stored externally | Business assumption | Accepted | Cannot verify in SUT. Overview always shows "SauceCard #31337". |
| A-03 | Address stored externally | Business assumption | Accepted | Cannot verify in SUT. Only a postal code is collected. |
| A-04 | Shipping is a single free option | Live site, session 01 | Confirmed | "Free Pony Express Delivery!"; adds nothing to the total. |
| A-05 | Tax rate calculation | Live site, session 01 (6 carts, $7.99 to $129.94) | Confirmed | Tax = 8% of item total, rounded to 2 dp. Rounding method at exactly .005 not yet observed. Display issue logged separately as DEF-008. |
| A-06 | 6 products, no stock limits | Live site, session 01 | Confirmed | Backpack $29.99, Bike Light $9.99, Bolt T-Shirt $15.99, Fleece Jacket $49.99, Onesie $7.99, Test.allTheThings() T-Shirt (Red) $15.99. |
| A-07 | Single currency | Live site, session 01 | Confirmed | All prices in US dollars. |
| A-09 | Cart persists after logout | Live site, session 01 | Confirmed (worse than assumed) | Cart is stored in the browser, not per user. The next user on the same browser sees it (DEF-006). |
| A-10 | Error user and visual user behaviour | Live site, session 01 | Confirmed | `problem_user`: broken images, sort, add/remove, last-name field; can't buy. `error_user`: sort popup, add/remove, last name, Finish fail; can't buy. `visual_user`: wrong prices shown, broken image, cart icon moved; charged real prices. `performance_glitch_user`: 5.1 s login (measured once). |
