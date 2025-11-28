# Playwright 

## What is Playwright
**Playwright** is a modern, flexible tool for end-to-end web testing. What makes it stand out:

- **Cross-browser & Cross-platform:** Test on Chromium, WebKit, and Firefox across Windows, Linux, and macOS—locally or in CI, headless or headed.
- **Multiple Languages:** Use Playwright with TypeScript, JavaScript, Python, .NET, or Java.
- **Mobile Web Testing:** Emulate Chrome for Android and Mobile Safari, using the same engine as desktop.
- **Reliable Tests:** Built-in auto-waiting and web-first assertions help eliminate flaky tests
- **Rich Debugging:** Capture traces, videos, and screenshots to troubleshoot and improve test reliability.
- **No Limits:** Run tests across multiple tabs, origins, and users. Playwright supports complex scenarios, including Shadow DOM and frames.
- **Full Isolation:** Each test runs in its own browser context (like a fresh profile), ensuring clean, fast, and independent runs.
- **Efficient Authentication:** Log in once, save the state, and reuse it across tests for speed and consistency.

## Installing Playwright

To install Playwright, open your console and run:

```sh
npm init playwright@latest
```

This command sets up Playwright in your project. You’ll be prompted to pick TypeScript or JavaScript, set a tests folder, and optionally add CI workflow files. Playwright will also download browser binaries (Chromium, Firefox, WebKit) so you can test across all major browsers right away.

**What gets installed:**
- `playwright.config.ts` – Central place for test settings (browsers, timeouts, retries, etc.)
- `package.json` and lock file – Project dependencies
- `tests/` – Minimal starter test
- Browser binaries – Chromium, Firefox, and WebKit for local and CI testing

## Browser Management

Playwright lets you configure multiple "projects" in your test setup, making it easy to run the same tests across different browsers, devices, or configurations. Each project can specify its own browser, device emulation, or custom settings. This is managed in your `playwright.config.ts` file.

**Example: Running tests in multiple browsers and devices**
```ts
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  projects: [
    { name: 'chromium', use: devices['Desktop Chrome'] },
    { name: 'firefox', use: devices['Desktop Firefox'] },
    { name: 'webkit', use: devices['Desktop Safari'] },
    { name: 'Mobile Chrome', use: devices['Pixel 5'] },
    { name: 'Mobile Safari', use: devices['iPhone 12'] },
  ],
});
```
With this setup, Playwright will automatically run your tests in all specified browsers and devices.

### Using Local Browser

By default, Playwright downloads and manages its own browser binaries for reliability and consistency. However, you can point Playwright to a locally installed browser if needed (for example, for testing a custom build or a specific browser version). To do this, set the `executablePath` option in your project’s `use` configuration:

```ts
// Example: Use a local Chrome installation
{
  name: 'local-chrome',
  use: {
    channel: undefined, // unset Playwright-managed channel
    executablePath: 'C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe', // Windows path example
  },
}
```

## Playwright Test

Playwright tests are simple in concept: you perform actions and make assertions. Playwright automatically waits for elements to be ready, so you don’t need manual waits.

### First Test Example (taken from Playwright Docs)

```js
import { test, expect } from '@playwright/test';

test('has title', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  await expect(page).toHaveTitle(/Playwright/);
});

test('get started link', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  await page.getByRole('link', { name: 'Get started' }).click();
  await expect(page.getByRole('heading', { name: 'Installation' })).toBeVisible();
});
```

### Actions, Assertions, and Test Isolation

- **Actions & Navigation:**  
  - Navigate to pages with `page.goto()`
  - Interact with elements using locators (e.g., click, fill, check/uncheck, hover, select options)
  - Most actions are available as methods on locators

- **Assertions:**  
  - Use `expect()` for checks (e.g., visibility, text, title, URL)
  - Async matchers wait for conditions, reducing flakiness
  - Generic matchers like `toEqual`, `toContain`, and `toBeTruthy` are also available

- **Test Isolation:**  
  - Each test runs in its own isolated browser context (like a fresh profile)
  - Ensures tests don’t interfere with each other and remain reliable

## Element Location & Selection

Playwright offers powerful and flexible ways to find and interact with elements on the page. Locators are the main way to select elements for actions and assertions.

### Locator Basics

- **Locators** are used to find elements and perform actions/assertions on them.
- Playwright recommends using [web-first assertions](https://playwright.dev/docs/test-assertions) and built-in locator strategies for reliability.

### Common Locator Methods

- `page.getByRole(role, options)`  
  Find elements by their ARIA role (e.g., button, link, heading).  
  ```js
  await page.getByRole('button', { name: 'Submit' }).click();
  ```

- `page.getByText(text, options)`  
  Find elements by their visible text.  
  ```js
  await page.getByText('Welcome').isVisible();
  ```

- `page.getByLabel(label, options)`  
  Find form controls by associated label text.  
  ```js
  await page.getByLabel('Email').fill('user@example.com');
  ```

- `page.getByPlaceholder(placeholder, options)`  
  Find input fields by placeholder text.  
  ```js
  await page.getByPlaceholder('Search').type('Playwright');
  ```

- `page.getByTestId(testId)`  
  Find elements by a `data-testid` attribute (great for stable selectors).  
  ```js
  await page.getByTestId('login-button').click();
  ```

### CSS and XPath Selectors

- You can also use standard CSS or XPath selectors:
  ```js
  // By class
  await page.locator('.my-class').click();

  // By id
  await page.locator('#main-header').isVisible();

  // By tag
  await page.locator('button').first().click();

  // By attribute
  await page.locator('input[type="email"]').fill('user@example.com');

  // By descendant
  await page.locator('.form-section input[type="password"]').fill('secret');

  // By multiple classes
  await page.locator('button.primary.cta').click();

  // XPath example
  await page.locator('//button[text()="OK"]').click();
  ```

### Chaining and Scoping

Playwright locators can be chained, filtered, and scoped to precisely target elements.

- **Locator Chaining:**  
  Chain locators to drill down to specific elements.
  ```js
  // Find a list, then a specific item in that list
  await page.getByRole('list').getByText('Item 1').click();
  ```

- **Filtering with Options:**  
  Use options like `name`, `hasText`, or `has` to filter results.
  ```js
  // Find a button with specific text
  await page.getByRole('button', { name: 'Save' }).click();
  ```

- **Scoping Locators:**  
  Scope your search to a section of the page for more reliable targeting.
  ```js
  const sidebar = page.getByTestId('sidebar');
  await sidebar.getByRole('link', { name: 'Settings' }).click();
  ```

- **Advanced Filtering:**  
  Use the `filter()` method to refine locator selection.
  ```js
  // Find a table row with a specific cell value, then click a button in that row
  await page.locator('tr').filter({ hasText: 'John Doe' }).locator('button.edit').click();
  ```

- **Selecting by Position:**  
  Use `.first()`, `.nth(index)`, and `.last()` to pick elements by order.
  ```js
  // Click the first button with a certain class
  await page.locator('button.action').first().click();

  // Click the third item in a list (index is zero-based)
  await page.locator('ul > li').nth(2).click();

  // Click the last visible "Delete" button
  await page.locator('button.delete').last().click();
  ```

### Best Practices
- Prefer role, label, and test ID locators for stability and readability.
- Avoid brittle selectors like deeply nested CSS or XPath unless necessary.

## Element Interaction

Playwright can automate a wide range of user actions on web elements. Most actions are available as methods on locators, making your tests expressive and reliable.

### Common Element Actions

- **Clicking:**  
  Click buttons, links, or any clickable element.
  ```js
  await page.getByRole('button', { name: 'Submit' }).click();
  await page.locator('.my-link').click();
  ```

- **Filling Inputs:**  
  Fill text fields, textareas, or other input elements.
  ```js
  await page.getByLabel('Email').fill('user@example.com');
  await page.locator('input[name="username"]').fill('myUser');
  ```

- **Typing:**  
  Type text into an input, simulating real keystrokes. Use this when you need to test how individual key inputs are handled.
  ```js
  await page.getByPlaceholder('Search').type('Playwright');
  ```

- **Checking/Unchecking:**  
  Toggle checkboxes and radio buttons.
  ```js
  await page.getByLabel('Accept Terms').check();
  await page.getByLabel('Subscribe').uncheck();
  ```

- **Selecting Options:**  
  Choose options from a `<select>` dropdown.
  ```js
  await page.getByLabel('Country').selectOption('US');
  ```

- **Hovering:**  
  Move the mouse over an element (e.g., to reveal tooltips or menus).
  ```js
  await page.getByText('More Info').hover();
  ```

- **Focusing and Blurring:**  
  Programmatically focus or blur elements. Useful during debugging
  ```js
  await page.getByLabel('Email').focus();
  await page.getByLabel('Email').blur();
  ```

- **Pressing Keys:**  
  Simulate keyboard shortcuts or key presses.
  ```js
  await page.getByLabel('Search').press('Enter');
  await page.locator('body').press('Control+A');
  ```

- **Dragging and Dropping:**  
  Drag elements and drop them onto targets.
  ```js
  await page.locator('#item').dragTo(page.locator('#dropzone'));
  ```

## Assertions via Expect

Playwright uses the `expect` API for assertions, making it easy to check the state of elements and pages. These assertions are "web-first," meaning they automatically wait for the expected condition to be true before proceeding, which helps reduce flaky tests.

### Common Assertions

- **Visibility:**  
  Check if an element is visible or hidden.
  ```js
  await expect(page.getByText('Welcome')).toBeVisible();
  await expect(page.getByText('Loading')).not.toBeVisible();
  ```

- **Text Content:**  
  Assert that an element contains specific text.
  ```js
  await expect(page.getByRole('heading')).toHaveText('Dashboard'); // Exact match
  await expect(page.getByLabel('Error')).toContainText('Invalid'); // Substring match
  ```

- **Attribute Value:**  
  Check for specific attribute values.
  ```js
  await expect(page.getByLabel('Email')).toHaveAttribute('type', 'email');
  ```

- **Input Value:**  
  Assert the value of an input or textarea.
  ```js
  await expect(page.getByLabel('Email')).toHaveValue('user@example.com');
  ```

- **Page Title and URL:**  
  Check the page title or URL.
  ```js
  await expect(page).toHaveTitle(/Playwright/);
  await expect(page).toHaveURL('https://playwright.dev/');
  ```

- **State Assertions:**  
  Check if an element is enabled, checked, or editable.
  ```js
  await expect(page.getByLabel('Accept Terms')).toBeChecked();
  await expect(page.getByLabel('Submit')).toBeEnabled();
  await expect(page.getByLabel('Username')).toBeEditable();
  ```

- **Count Assertions:**  
  Assert the number of matching elements.
  ```js
  await expect(page.locator('.notification')).toHaveCount(3);
  ```

- **Generic Matchers:**  
  Use matchers like `toEqual`, `toContain`, or `toBeTruthy` for immediate checks.
  ```js
  expect([1, 2, 3]).toContain(2);
  expect(result).toBeTruthy();
  ```

### Notes

- You can chain `.not` for negative assertions.
- Playwright has [auto-retrying assertions](https://playwright.dev/docs/test-assertions#auto-retrying-assertions) for assertions that requiring checking the state of the page, and [non-retrying assertions](https://playwright.dev/docs/test-assertions#non-retrying-assertions) that are closer to standard code assertions (collection contains an element, is truthy, etc.).

## Fixtures in Playwright

**Fixtures** in Playwright are reusable building blocks that provide context and resources to your tests. They help you set up things like browser pages, test data, or authentication before each test runs, and clean up afterward. Fixtures make tests more modular, maintainable, and DRY (Don’t Repeat Yourself).

### Common Built-in Fixtures

Playwright provides several fixtures out of the box:

- `page` – A new browser tab for each test (isolated context)
- `browser` – The browser instance itself
- `context` – The browser context (like a fresh user profile)
- `request` – APIRequestContext for making HTTP requests

### Using Fixtures in Your Tests

You inject fixtures by destructuring them from the test function’s arguments:

```js
import { test, expect } from '@playwright/test';

test('uses the page fixture', async ({ page }) => {
  await page.goto('https://playwright.dev/');
  await expect(page).toHaveTitle(/Playwright/);
});

test('uses context and browser', async ({ context, browser }) => {
  const page = await context.newPage();
  // ...test code...
});
```

### Creating Custom Fixtures

You can define your own fixtures to share setup/teardown logic or resources across tests. Custom fixtures are typicallydefined in a separate file using `test.extend()`:

```js
import { test as base, expect } from '@playwright/test';

export const test = base.extend({
  // Define a custom fixture
  myUser: async ({}, use) => {
    const user = { name: 'Alice', role: 'admin' };
    await use(user); // makes `myUser` available in tests
  },
});
```
```js
import { test } from 'myfile.js'
test('uses custom fixture', async ({ myUser }) => {
  expect(myUser.name).toBe('Alice');
  // ...test code...
});
```

The `use` function is how you provide the fixture value to your tests. You call `await use(resource)` inside your fixture definition, and that resource becomes available as an argument in your test similar to fixtures like `page` and `request`. This also allows you to run setup code before and cleanup code after the test if needed. You can create fixtures for anything your tests need, such as:
- Test users or authentication tokens
- Mock servers or API clients
- Database connections or seeded data
- Custom browser contexts or pages with special settings
- Temporary files or directories

Fixtures help keep your tests DRY, modular, and easy to maintain.

### Notes

- Fixtures can be async and support setup/teardown logic.
- You can override built-in fixtures if needed.
- Use fixtures to keep tests clean and avoid repeating setup code.

## APIRequestContext

Playwright’s `APIRequestContext` fixture allows you to make HTTP requests directly from your tests, which is useful for setup, teardown, or validating backend APIs alongside UI tests.

### Making Requests

You can access the `request` fixture in your test and use it to send HTTP requests:

```js
import { test, expect } from '@playwright/test';

test('make a GET request', async ({ request }) => {
  const response = await request.get('https://api.example.com/users/1');
  expect(response.ok()).toBeTruthy();
});
```

### Common Request Settings

You can customize requests with options such as method, headers, body, and query params:

```js
// POST with JSON body and custom headers
const response = await request.post('https://api.example.com/login', {
  data: { username: 'user', password: 'pass' },
  headers: { 'Authorization': 'Bearer token', 'Content-Type': 'application/json' },
});

// PUT with form data
const response2 = await request.put('https://api.example.com/profile', {
  form: { name: 'Alice', age: 30 }
});

// GET with query parameters
const response3 = await request.get('https://api.example.com/search', {
  params: { q: 'playwright', page: 2 }
});
```

- **data:** Send JSON payloads.
- **form:** Send form-encoded data.
- **headers:** Set custom headers.
- **params:** Add query parameters.

### Receiving and Unpacking Responses

You can inspect the response status, headers, and body:

```js
const response = await request.get('https://api.example.com/data');
expect(response.status()).toBe(200);

const json = await response.json(); // Parse JSON response
expect(json.id).toBe(1);

const text = await response.text(); // Get raw text
const headers = response.headers(); // Get response headers
```

### Notes

- `APIRequestContext` is great for pre-populating test data, cleaning up, or validating APIs.
- You can create isolated API contexts with custom base URLs, authentication, or cookies if needed.
- Use API requests to speed up test setup and avoid slow UI flows when possible.
