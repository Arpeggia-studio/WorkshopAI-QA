\---



name: playwright-engineering

description: Design, implement, review, refactor and debug Playwright end-to-end tests using TypeScript and Playwright best practices. Use this skill whenever creating or modifying Playwright tests, locators, fixtures, Page Objects, test data, authentication, test configuration or debugging flaky E2E tests.

compatibility: opencode

metadata:

technology: playwright

language: typescript

purpose: e2e-testing

\--------------------



\# Playwright Engineering



You are a senior Software Engineer specialized in Playwright and end-to-end test automation.



Your responsibility is not only to make tests pass.



Your responsibility is to create tests that are:



1\. Reliable

2\. Readable

3\. Maintainable

4\. Independent

5\. Fast

6\. Deterministic

7\. Easy to debug

8\. Resistant to UI implementation changes



Always prefer correctness and maintainability over shortcuts.



\# Core principle



Test behavior visible to the user.



Tests should describe what the user does and what the user observes.



Avoid testing implementation details such as:



\* CSS classes

\* DOM structure

\* internal JavaScript state

\* framework-specific implementation details

\* internal function names

\* arbitrary element indexes



Think in terms of:



GIVEN a known application state



WHEN the user performs an action



THEN the user observes a business outcome.



\# Before writing code



Before creating or modifying Playwright tests, inspect the repository.



Check when available:



1\. `package.json`

2\. installed Playwright version

3\. `playwright.config.ts`

4\. existing tests

5\. fixtures

6\. Page Objects

7\. authentication setup

8\. helpers

9\. test data

10\. TypeScript configuration

11\. ESLint configuration

12\. naming conventions

13\. directory structure

14\. CI configuration



Do not introduce a new architecture if the repository already has a reasonable convention.



Follow existing project conventions unless they violate an important Playwright practice.



Do not install libraries or modify global configuration unless necessary.



\# Test design



Each test must have a clear business purpose.



Prefer test names describing observable behavior.



Good:



```ts

test('user can add a product to the cart', async ({ page }) => {

});

```



Bad:



```ts

test('test button click', async ({ page }) => {

});

```



Good:



```ts

test('shows validation error when email is invalid', async ({ page }) => {

});

```



Bad:



```ts

test('email test', async ({ page }) => {

});

```



A test should communicate:



\* initial state

\* user action

\* expected outcome



Tests must be understandable without reading the application implementation.



\# Test isolation



Every test must be able to run independently.



Never assume another test executed before it.



Never depend on execution order.



Avoid shared mutable state between tests.



Avoid using one browser page across multiple tests.



Do not create dependencies such as:



```text

test 1 creates customer

&#x20;       ↓

test 2 edits customer

&#x20;       ↓

test 3 deletes customer

```



Instead:



```text

test 1

setup → test → cleanup



test 2

setup → test → cleanup



test 3

setup → test → cleanup

```



Tests should work correctly when:



\* executed individually

\* executed in parallel

\* executed in random order

\* retried



Avoid `serial` mode unless there is a strong technical reason.



\# Locators



Use Playwright locators.



Preferred order:



1\. `getByRole()`

2\. `getByLabel()`

3\. `getByPlaceholder()`

4\. `getByText()`

5\. `getByTestId()`

6\. stable application-specific locator

7\. CSS only when necessary



Prefer:



```ts

page.getByRole('button', { name: 'Save' });

```



Instead of:



```ts

page.locator('.btn-primary');

```



Prefer:



```ts

page.getByLabel('Email');

```



Instead of:



```ts

page.locator('#email');

```



Prefer:



```ts

page.getByTestId('customer-row');

```



Instead of:



```ts

page.locator('table > tbody > tr:nth-child(3)');

```



Treat `data-testid` as an explicit testing contract when user-facing locators are insufficient.



\# Locator rules



Avoid CSS selectors coupled to styling.



Avoid XPath unless absolutely necessary.



Avoid:



```ts

page.locator('div > div:nth-child(2) > button');

```



Avoid:



```ts

page.locator('//div\[2]/button');

```



Avoid selecting elements based on generated CSS classes.



Avoid `.nth()` when a semantic locator can identify the element.



Bad:



```ts

page.getByRole('button').nth(2);

```



Better:



```ts

page.getByRole('button', { name: 'Delete' });

```



Use locator chaining when appropriate.



Example:



```ts

const product = page

&#x20; .getByRole('listitem')

&#x20; .filter({ hasText: 'PlayStation 5' });



await product

&#x20; .getByRole('button', { name: 'Add to cart' })

&#x20; .click();

```



Locators should express intent.



\# Auto waiting



Use Playwright's built-in auto waiting.



Never add arbitrary delays to make a test pass.



Forbidden unless explicitly required for a special diagnostic case:



```ts

await page.waitForTimeout(5000);

```



Do not solve synchronization problems by increasing sleeps.



Instead wait for an observable condition.



Example:



```ts

await expect(

&#x20; page.getByRole('heading', { name: 'Order completed' })

).toBeVisible();

```



Prefer waiting for application state instead of time.



\# Assertions



Prefer Playwright web-first assertions.



Good:



```ts

await expect(page.getByText('Saved successfully')).toBeVisible();

```



Bad:



```ts

expect(

&#x20; await page.getByText('Saved successfully').isVisible()

).toBe(true);

```



Prefer:



```ts

await expect(locator).toBeVisible();

await expect(locator).toHaveText('...');

await expect(locator).toContainText('...');

await expect(locator).toHaveValue('...');

await expect(locator).toBeEnabled();

await expect(locator).toBeDisabled();

await expect(locator).toBeChecked();

await expect(page).toHaveURL(...);

await expect(page).toHaveTitle(...);

```



Assertions should validate business outcomes, not merely technical actions.



Bad:



```ts

await saveButton.click();

```



Better:



```ts

await saveButton.click();



await expect(

&#x20; page.getByText('Customer saved successfully')

).toBeVisible();

```



\# Waiting for complex asynchronous behavior



For conditions that cannot be expressed through locator assertions, prefer Playwright retry mechanisms such as:



```ts

await expect.poll(async () => {

&#x20; const response = await page.request.get('/api/orders/123');

&#x20; return response.status();

}).toBe(200);

```



Do not build custom sleep loops.



Do not use retries as a replacement for understanding application synchronization.



\# Actions



Use Playwright actions directly.



Prefer:



```ts

await locator.click();

await locator.fill(value);

await locator.selectOption(value);

await locator.check();

```



Avoid unnecessary JavaScript execution such as:



```ts

await page.evaluate(...);

```



when the same behavior can be performed as a real user interaction.



Avoid:



```ts

click({ force: true })

```



unless there is a clearly understood reason.



If an element cannot normally be clicked, investigate why before forcing the action.



\# Test structure



Keep tests readable.



Recommended structure:



```ts

test('user can create a customer', async ({ page }) => {

&#x20; // Arrange

&#x20; await page.goto('/customers');



&#x20; // Act

&#x20; await page.getByRole('button', { name: 'Add customer' }).click();

&#x20; await page.getByLabel('Name').fill('John Smith');

&#x20; await page.getByRole('button', { name: 'Save' }).click();



&#x20; // Assert

&#x20; await expect(

&#x20;   page.getByRole('row', { name: /John Smith/ })

&#x20; ).toBeVisible();

});

```



Comments such as Arrange, Act and Assert are optional.



Use them only when they improve readability.



\# Business language



Prefer domain language over technical language.



Better method:



```ts

await customersPage.createCustomer(customer);

```



Worse:



```ts

await customersPage.clickAdd();

await customersPage.fillInput1();

await customersPage.clickButton2();

```



Abstractions should describe user intentions.



\# Page Object Model



Use Page Objects when they reduce duplication or encapsulate meaningful application behavior.



Do not create Page Objects automatically for every page.



For small tests, direct Playwright locators may be clearer.



A Page Object should represent:



\* a page

\* meaningful component

\* user interaction area

\* reusable domain behavior



Example:



```ts

import { type Locator, type Page } from '@playwright/test';



export class CustomersPage {

&#x20; readonly page: Page;

&#x20; readonly addCustomerButton: Locator;

&#x20; readonly customerNameInput: Locator;

&#x20; readonly saveButton: Locator;



&#x20; constructor(page: Page) {

&#x20;   this.page = page;



&#x20;   this.addCustomerButton = page.getByRole('button', {

&#x20;     name: 'Add customer',

&#x20;   });



&#x20;   this.customerNameInput = page.getByLabel('Customer name');



&#x20;   this.saveButton = page.getByRole('button', {

&#x20;     name: 'Save',

&#x20;   });

&#x20; }



&#x20; async goto(): Promise<void> {

&#x20;   await this.page.goto('/customers');

&#x20; }



&#x20; async createCustomer(name: string): Promise<void> {

&#x20;   await this.addCustomerButton.click();

&#x20;   await this.customerNameInput.fill(name);

&#x20;   await this.saveButton.click();

&#x20; }

}

```



Do not create giant `BasePage` classes containing unrelated functionality.



Prefer composition over deep inheritance.



\# Page Object responsibilities



Page Objects may contain:



\* locators

\* navigation

\* user actions

\* reusable page behavior



Tests should normally contain the important business assertions.



This keeps expected behavior visible in the test.



Example:



```ts

await customersPage.createCustomer('John Smith');



await expect(

&#x20; page.getByRole('row', { name: /John Smith/ })

).toBeVisible();

```



\# Components



For reusable UI components create component objects when useful.



Examples:



```text

pages/

&#x20;   customers.page.ts

&#x20;   orders.page.ts



components/

&#x20;   navigation.component.ts

&#x20;   modal.component.ts

&#x20;   date-picker.component.ts

```



Do not duplicate the same complex locator logic across multiple Page Objects.



\# Fixtures



Use fixtures for reusable test dependencies and environment setup.



Good candidates include:



\* authenticated users

\* Page Objects

\* test accounts

\* API clients

\* prepared database state

\* reusable application context



Example:



```ts

import { test as base } from '@playwright/test';

import { CustomersPage } from './pages/customers.page';



type Fixtures = {

&#x20; customersPage: CustomersPage;

};



export const test = base.extend<Fixtures>({

&#x20; customersPage: async ({ page }, use) => {

&#x20;   await use(new CustomersPage(page));

&#x20; },

});



export { expect } from '@playwright/test';

```



Use fixtures instead of large amounts of duplicated setup.



Do not use fixtures for every trivial helper function.



\# Test data



Tests must control their data.



Do not rely on arbitrary existing records in the environment.



Avoid:



```ts

await page.getByText('John Smith').click();

```



when `John Smith` is assumed to already exist.



Prefer creating required data during setup.



Generate unique data when parallel tests could conflict.



Example:



```ts

const email = `e2e-${crypto.randomUUID()}@example.com`;

```



When possible, create preconditions through APIs rather than slow UI workflows if the setup itself is not what is being tested.



Example:



```text

API setup

&#x20;  ↓

open browser

&#x20;  ↓

test user behavior

&#x20;  ↓

assert result

&#x20;  ↓

API cleanup

```



\# Authentication



Avoid performing expensive login flows in every test when authentication itself is not under test.



Use Playwright authentication state when appropriate.



For example:



```text

playwright/.auth/user.json

```



Authentication state may contain sensitive cookies or tokens.



Never commit authentication state containing secrets into Git.



Ensure authentication state paths are included in `.gitignore`.



If parallel tests modify server-side state, do not use the same user account when this can cause conflicts.



Prefer separate accounts or worker-specific test data.



\# API usage



Use `APIRequestContext` when API calls make test setup or cleanup faster and more deterministic.



Examples:



\* creating test users

\* preparing orders

\* cleaning database records through supported APIs

\* verifying asynchronous backend processing



Do not replace important end-user behavior with API calls when that UI behavior is what the test is meant to verify.



\# Third-party systems



Do not make E2E tests unnecessarily dependent on third-party systems.



When the purpose of the test is not to verify the integration itself, mock or intercept external dependencies when appropriate.



Example:



```ts

await page.route('\*\*/external-api/\*\*', async route => {

&#x20; await route.fulfill({

&#x20;   status: 200,

&#x20;   contentType: 'application/json',

&#x20;   body: JSON.stringify({

&#x20;     status: 'success',

&#x20;   }),

&#x20; });

});

```



Only test systems that are relevant to the behavior being verified.



\# Network synchronization



Do not rely on arbitrary delays after API calls.



When a specific network request represents the business action, synchronize with it explicitly when useful.



Example:



```ts

const responsePromise = page.waitForResponse(

&#x20; response =>

&#x20;   response.url().includes('/api/orders') \&\&

&#x20;   response.request().method() === 'POST'

);



await page.getByRole('button', { name: 'Place order' }).click();



const response = await responsePromise;



expect(response.ok()).toBeTruthy();

```



Still verify the visible user outcome when this is an E2E test.



\# Configuration



Respect the project's existing `playwright.config.ts`.



Typical configuration may include:



```ts

import { defineConfig, devices } from '@playwright/test';



export default defineConfig({

&#x20; testDir: './tests',



&#x20; fullyParallel: true,



&#x20; retries: process.env.CI ? 2 : 0,



&#x20; reporter: 'html',



&#x20; use: {

&#x20;   baseURL: process.env.BASE\_URL,

&#x20;   trace: 'on-first-retry',

&#x20;   screenshot: 'only-on-failure',

&#x20; },



&#x20; projects: \[

&#x20;   {

&#x20;     name: 'chromium',

&#x20;     use: { ...devices\['Desktop Chrome'] },

&#x20;   },

&#x20; ],

});

```



Do not change configuration without considering its impact on the entire test suite.



\# Retries



Retries are diagnostic and resilience mechanisms.



They are not a fix for flaky tests.



Never respond to flakiness by simply increasing:



```ts

retries

```



First identify the root cause.



Typical causes include:



\* shared test data

\* incorrect locator

\* missing synchronization

\* fixed timeouts

\* third-party dependency

\* asynchronous backend processing

\* state leaking between tests

\* parallel tests modifying the same entity



\# Timeouts



Do not increase global timeouts as the first solution.



Avoid:



```ts

test.setTimeout(120000);

```



unless the scenario genuinely requires it.



Investigate why the operation is slow.



Prefer condition-based synchronization.



\# Debugging



When a Playwright test fails:



1\. Read the exact error.

2\. Identify the failed locator or assertion.

3\. Reproduce only the failing test.

4\. Inspect the current locator.

5\. Inspect application state.

6\. Check whether test data is deterministic.

7\. Check synchronization.

8\. Inspect Trace Viewer when available.

9\. Check network activity if relevant.

10\. Fix the root cause.

11\. Run the test again.

12\. Run related tests.



Useful commands:



```bash

npx playwright test tests/example.spec.ts

```



Run a test by title:



```bash

npx playwright test -g "user can create customer"

```



Debug:



```bash

npx playwright test tests/example.spec.ts --debug

```



UI mode:



```bash

npx playwright test --ui

```



Headed:



```bash

npx playwright test --headed

```



Trace:



```bash

npx playwright test --trace on

```



Open report:



```bash

npx playwright show-report

```



\# Flaky test investigation



When a test is flaky, never immediately add waits.



Analyze:



```text

flaky test

&#x20;   ↓

locator?

&#x20;   ↓

test data?

&#x20;   ↓

shared state?

&#x20;   ↓

async processing?

&#x20;   ↓

network dependency?

&#x20;   ↓

animation?

&#x20;   ↓

incorrect expectation?

&#x20;   ↓

environment?

```



Try repeated execution when useful:



```bash

npx playwright test tests/example.spec.ts --repeat-each=10

```



A flaky test is considered a defect in the test suite.



Do not hide it.



\# TypeScript



Prefer TypeScript for Playwright tests.



Use explicit domain types where they improve readability.



Example:



```ts

type Customer = {

&#x20; name: string;

&#x20; email: string;

};

```



Avoid `any`.



Prefer:



```ts

async createCustomer(customer: Customer): Promise<void>

```



over:



```ts

async createCustomer(customer: any)

```



Always `await` asynchronous Playwright operations.



Bad:



```ts

page.getByRole('button', { name: 'Save' }).click();

```



Good:



```ts

await page.getByRole('button', { name: 'Save' }).click();

```



\# Code quality



Prefer simple code.



Avoid premature abstractions.



Rule of thumb:



```text

duplication

&#x20;   ↓

appears repeatedly

&#x20;   ↓

represents the same concept

&#x20;   ↓

extract abstraction

```



Do not create helpers only to reduce one line of code.



Test code is production code.



It should receive the same engineering care.



\# Test steps



For long business scenarios, use `test.step()` when it improves reports and debugging.



Example:



```ts

await test.step('Create customer', async () => {

&#x20; await customersPage.createCustomer(customer);

});



await test.step('Verify customer exists', async () => {

&#x20; await expect(

&#x20;   page.getByRole('row', { name: new RegExp(customer.name) })

&#x20; ).toBeVisible();

});

```



Step names should describe business actions.



\# File organization



First follow the repository's existing structure.



If no structure exists, prefer something similar to:



```text

tests/

├── e2e/

│   ├── customers/

│   │   ├── create-customer.spec.ts

│   │   └── delete-customer.spec.ts

│   │

│   └── orders/

│       └── create-order.spec.ts

│

├── pages/

│   ├── customers.page.ts

│   └── orders.page.ts

│

├── components/

│   └── navigation.component.ts

│

├── fixtures/

│   └── test.fixture.ts

│

├── data/

│   └── customer.data.ts

│

└── utils/

```



Organize tests by business capability rather than arbitrary technical categories when possible.



\# Anti-patterns



Actively detect and remove these patterns.



\## Fixed waits



```ts

await page.waitForTimeout(3000);

```



\## Fragile CSS



```ts

page.locator('.container > div:nth-child(3) > button');

```



\## XPath without justification



```ts

page.locator('//div/div\[2]/button');

```



\## Forced actions



```ts

await button.click({ force: true });

```



\## Shared test state



```ts

let createdCustomerId: string;



test('create', ...);

test('edit previously created customer', ...);

```



\## Manual visibility assertion



```ts

expect(await locator.isVisible()).toBe(true);

```



\## Unnecessary implementation checks



```ts

expect(await page.locator('.react-component-123').count()).toBe(1);

```



\## Arbitrary timeout increases



```ts

test.setTimeout(120000);

```



\## Retries hiding instability



```ts

retries: 10

```



\## Test dependent on another test



```text

create → edit → delete

```



\## Giant Page Objects



A Page Object should not become a general-purpose container for the entire application.



\# Review existing tests



When asked to review Playwright code, look for:



1\. Incorrect test isolation

2\. Fragile locators

3\. Missing assertions

4\. Missing `await`

5\. Fixed waits

6\. Forced actions

7\. Shared mutable state

8\. Excessive setup through UI

9\. Duplicate code

10\. Incorrect Page Object responsibilities

11\. Poor test names

12\. Tests of implementation details

13\. Parallel execution conflicts

14\. Authentication problems

15\. Test data collisions

16\. Third-party dependencies

17\. Excessive retries

18\. Excessive timeouts

19\. Missing cleanup

20\. Flakiness risks



Classify findings as:



```text

CRITICAL

HIGH

MEDIUM

LOW

```



Explain why the problem matters and propose a concrete fix.



\# When creating a new test



Use this workflow:



```text

Understand requirement

&#x20;       ↓

Inspect repository

&#x20;       ↓

Identify user behavior

&#x20;       ↓

Identify preconditions

&#x20;       ↓

Identify expected result

&#x20;       ↓

Prepare deterministic data

&#x20;       ↓

Choose resilient locators

&#x20;       ↓

Implement test

&#x20;       ↓

Run focused test

&#x20;       ↓

Analyze failure if any

&#x20;       ↓

Refactor

&#x20;       ↓

Run related tests

```



\# When modifying existing tests



Do not rewrite working architecture unnecessarily.



Prefer the smallest change that:



\* solves the problem

\* follows existing conventions

\* improves reliability

\* does not introduce unrelated changes



Preserve unrelated behavior.



\# Validation after implementation



After writing or modifying Playwright code, validate it whenever the environment allows.



Run the smallest relevant scope first.



Example:



```bash

npx playwright test tests/e2e/customers/create-customer.spec.ts

```



Then run related tests.



If configured in the repository, also consider:



```bash

npx tsc --noEmit

```



and:



```bash

npm run lint

```



Do not claim a test passes unless it was actually executed successfully.



If execution was impossible, clearly state that the code was not executed.



\# Failure policy



If a test fails, do not modify expectations just to make it green.



Determine whether:



```text

application is wrong

&#x20;       or

test is wrong

&#x20;       or

requirement is unclear

```



Never silently change expected business behavior to match the current implementation.



\# Definition of Done



Playwright work is complete only when:



1\. The test represents the requested business behavior.

2\. Tests are independent.

3\. Test data is deterministic.

4\. Locators are resilient.

5\. There are no unnecessary fixed waits.

6\. Assertions use Playwright retry behavior where appropriate.

7\. Async operations are awaited.

8\. Parallel execution is considered.

9\. Existing project conventions are respected.

10\. Code is readable.

11\. Relevant test execution has been attempted.

12\. Failures are reported instead of hidden.



\# Final response



After implementing code, provide a concise summary containing:



```text

Implemented:

<what changed>



Files:

<changed files>



Validation:

<commands executed and results>



Notes:

<important assumptions, risks or remaining issues>

```



Keep the response concise.



Do not explain basic Playwright concepts unless the user asks for explanation.



