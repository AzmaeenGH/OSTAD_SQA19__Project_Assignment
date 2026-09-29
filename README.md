# OSTAD SQA19 — Module 17 Project Assignment
### Demo Web Shop — UI Automation & API Testing

Automated test suite covering UI (Playwright) and API (Postman/Newman) testing for two target systems:

- **UI under test:** https://demowebshop.tricentis.com/
- **API under test:** https://jsonplaceholder.typicode.com/users

---

## 1. Project Overview

This repository contains the full deliverable for the Module 17 assignment, split into three parts:

- **Part A — UI Automation:** Three Playwright test scenarios against the Demo Web Shop.
  - **Q1 — Invalid Login:** Attempts login with bad credentials, verifies the error message and that the user is not logged in.
  - **Q2 — Register + Add Product to Cart:** Registers a new customer, logs in, browses a category, selects a product, adds it to the cart, and verifies the product and quantity in the cart.
  - **Q3 — Product Search → E2E Checkout:** Searches for a product, verifies it in the results, opens it, increases the quantity, adds it to cart, agrees to terms, completes checkout, and verifies the order confirmation.
- **Part B — GitHub Workflow:** Version-controlled delivery with incremental commit history.
- **Part C — API Automation:** A Postman collection (run via Newman) validating GET and PUT requests against `jsonplaceholder.typicode.com/users`.

---

## 2. Tech Stack

| Area | Tooling |
|---|---|
| UI test runner | **Playwright** Test |
| UI test design | Page Object Model |
| UI reporting | Playwright HTML reporter + Allure |
| API testing | **Postman** collection, executed via **Newman** CLI |
| Runtime | Node.js v20+ |

---

## 3. Repository Structure

```
OSTAD_SQA19__Project_Assignment/
├── README.md
├── api-tests/
│   └── Module_17_Assignment.postman_collection.json
└── ui-tests/
    ├── pages/
    │   ├── BasePage.js
    │   ├── InvalidLogin.js
    │   ├── Register.js
    │   ├── LoginPage.js
    │   ├── AddProductToCart.js
    │   ├── SearchProduct.js
    │   └── Checkout.js
    ├── tests/
    │   ├── q1_invalidLogin.test.js
    │   ├── q2.1_register.test.js
    │   ├── q2.2_login.test.js
    │   ├── q2.3_addToCart.test.js
    │   ├── q3.1_searchProduct.test.js
    │   └── q3.2_checkout.test.js
    ├── report_screenshots/
    ├── playwright.config.js
    ├── package.json
    └── package-lock.json

```

Each UI test file is **self-contained**: it registers its own account (unique email generated per run), logs in, and runs its scenario end to end. There is no shared session/state file between test files, so any file can be run alone or all can be run together.

---

## 4. Setup

### Prerequisites
- Node.js v20 or later
- npm
- Java Runtime (JRE 8+) — required by the Allure command-line tool

### Install

```bash
git clone https://github.com/AzmaeenGH/OSTAD_SQA19__Project_Assignment.git
cd OSTAD_SQA19__Project_Assignment/ui-tests

npm install
npx playwright install --with-deps
```

Newman does not need a global install — it is invoked with `npx` directly (see section 5.2).

---

## 5. Running the Tests

Run all commands below from inside `ui-tests/` unless stated otherwise.

### 5.1 UI Tests (Playwright)

**Run everything (Q1 + Q2 + Q3, all browsers):**
```bash
npx playwright test
```

**Run a single scenario:**

| Scenario | Command |
|---|---|
| Q1 — Invalid Login | `npx playwright test tests/q1_invalidLogin.test.js` |
| Q2 — Register + Add to Cart | `npx playwright test tests/q2.1_register.test.js tests/q2.2_login.test.js tests/q2.3_addToCart.test.js` |
| Q3 — Search → Checkout | `npx playwright test tests/q3.1_searchProduct.test.js tests/q3.2_checkout.test.js` |



### 5.2 API Tests (Postman / Newman)

Run from the **repository root**:

```bash
npx newman run api-tests/Module_17_Assignment.postman_collection.json
```

The collection sets its own `base_url` via a collection-level pre-request script, so no separate environment file is required. Every request validates its status code, and Step 4 (PUT) validates the returned ID, non-empty phone, and updated name.

---

## 6. Generating Reports

### Playwright HTML Report
Generated automatically after any `playwright test` run, into `playwright-report/`.

```bash
npx playwright show-report
```

### Allure Report
Raw results are written to `allure-results/` on every run (configured in `playwright.config.js`). To build and view the human-readable report:

```bash
npx allure generate allure-results --clean -o allure-report
npx allure open allure-report
```

Or, for a quick one-off view without a separate generate step:

```bash
npx allure serve allure-results
```

Full-page screenshots are captured automatically on every test (`screenshot: 'on'` in `playwright.config.js`) and are embedded directly in both reports. `report_screenshots/` in the repo holds exported screenshots of a completed report run, kept as submission evidence.

---

## 7. Notes

- UI tests use dynamically generated emails (`sqa19testing_<timestamp>@gmail.com`) so registration never collides across runs.
- Selectors target the live `demowebshop.tricentis.com` markup; if the site's markup changes, locators in `pages/*.js` may need updating.
