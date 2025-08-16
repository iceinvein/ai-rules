# 🎭 Playwright Testing Rules

This document defines the rules and best practices for writing **end-to-end
(E2E) tests** with **Playwright** in this project.

Playwright tests verify **routing, authentication, role-based access, and
accessibility** in a real browser environment.

---

## 1. Folder Structure

All Playwright tests live under the `e2e/` directory.

```bash
e2e/
  ├── pages/              # Page Object Models (POMs)
  │   ├── login.page.ts
  │   ├── dashboard.page.ts
  │   └── admin.page.ts
  ├── specs/              # Test specs
  │   ├── auth.spec.ts
  │   ├── dashboard.spec.ts
  │   └── admin.spec.ts
  ├── fixtures/           # Custom fixtures (auth state, test data)
  │   └── auth.fixture.ts
  ├── utils/              # Helpers (e.g., random data, API helpers)
  │   └── test-helpers.ts
  └── playwright.config.ts
```

---

## 2. Page Object Model (POM)

We use the **Page Object Model** to encapsulate page selectors and actions.  
This keeps tests clean and reduces duplication.

(Examples in previous section)

---

## 3. Using POM in Tests

(Examples in previous section)

---

## 4. Fixtures

(Examples in previous section)

---

## 5. Utils

(Examples in previous section)

---

## 6. Naming Conventions

- **Specs**: `*.spec.ts` (e.g., `auth.spec.ts`, `dashboard.spec.ts`).
- **Pages**: `*.page.ts` (e.g., `login.page.ts`).
- **Fixtures**: `*.fixture.ts` (e.g., `auth.fixture.ts`).
- **Utils**: `*.ts` (e.g., `test-helpers.ts`).

---

## 7. Test Tagging & Grouping

We use **tags** to group tests into categories like `@smoke`, `@regression`,
`@auth`, etc. This allows selective execution in CI/CD pipelines.

### ✅ Rules

- Add tags in the test title (e.g., `"@smoke @auth user can login"`).
- Use tags consistently across the suite.
- Define **smoke tests** for critical paths (login, dashboard load).
- Define **regression tests** for full coverage.
- Use Playwright’s `grep` option to filter by tags.

---

### ✅ Example: Tagged Tests

```ts
import { test, expect } from "@playwright/test";

test("@smoke @auth user can login", async ({ page }) => {
  await page.goto("/login");
  await page.getByPlaceholder("Username").fill("admin");
  await page.getByRole("button", { name: /login/i }).click();
  await expect(page).toHaveURL("/dashboard");
});

test("@regression admin can access admin page", async ({ page }) => {
  await page.goto("/login");
  await page.getByPlaceholder("Username").fill("admin");
  await page.getByRole("button", { name: /login/i }).click();
  await page.goto("/admin");
  await expect(page.getByText(/admin page/i)).toBeVisible();
});
```

---

### ✅ Example: Running Tagged Tests

```bash
# Run only smoke tests
npx playwright test --grep "@smoke"

# Run only auth tests
npx playwright test --grep "@auth"

# Run everything except regression
npx playwright test --grep-invert "@regression"
```

---

### ✅ Example: playwright.config.ts

```ts
import { defineConfig } from "@playwright/test";

export default defineConfig({
  testDir: "./e2e/specs",
  use: {
    baseURL: "http://localhost:5173",
    headless: true,
  },
  // Example: run only smoke tests in CI
  grep: process.env.CI ? /@smoke/ : undefined,
});
```

---

## ✅ Do / ❌ Don’t Checklist

### ✅ Do

- Organize tests by **feature** (`auth.spec.ts`, `dashboard.spec.ts`).
- Use **POMs** for selectors and actions.
- Use **fixtures** for common setup (auth, seeded data).
- Use **utils** for reusable helpers.
- Keep tests **short and readable** (1–2 assertions per test).
- Prefer **data-testid** for stable selectors.
- Use **tags** (`@smoke`, `@auth`, `@regression`) for grouping.

### ❌ Don’t

- ❌ Put all tests in a single file.
- ❌ Repeat login steps in every test (use fixtures).
- ❌ Use brittle selectors (CSS classes, nth-child).
- ❌ Mix POM logic inside test specs.
- ❌ Write long “mega-tests” (split into smaller cases).
- ❌ Leave tests untagged (always categorize).

---

## 8. Test Independence & Parallel Execution

All Playwright tests must be **self-contained** and able to run in **isolation**.  
Tests should not depend on the outcome or side effects of other tests.  
This ensures stability, parallel execution, and reproducibility in CI/CD.

---

### ✅ Rules

- Each test must **set up its own state** (e.g., login, seed data).
- Do not assume a test will run before or after another.
- Use **fixtures** for common setup (e.g., `loginAsAdmin`).
- Use **storage state** or **API setup** to avoid repeating UI steps when possible.
- Clean up after tests if they modify shared state (e.g., reset DB, logout).
- Tests must be runnable:
  - Individually (`npx playwright test e2e/specs/auth.spec.ts`).
  - In parallel (`npx playwright test --workers=4`).
  - In random order (no hidden dependencies).

---

### ✅ Example: Bad (Dependent Tests)

```ts
import { test, expect } from "@playwright/test";

test("login as admin", async ({ page }) => {
  await page.goto("/login");
  await page.getByPlaceholder("Username").fill("admin");
  await page.getByRole("button", { name: /login/i }).click();
  await expect(page).toHaveURL("/dashboard");
});

// ❌ This test assumes the previous test already logged in
test("access dashboard", async ({ page }) => {
  await page.goto("/dashboard");
  await expect(page.getByText(/dashboard/i)).toBeVisible();
});
```

---

### ✅ Example: Good (Independent Tests)

```ts
import { test, expect } from "@playwright/test";

test("admin can login and see dashboard", async ({ page }) => {
  await page.goto("/login");
  await page.getByPlaceholder("Username").fill("admin");
  await page.getByRole("button", { name: /login/i }).click();
  await expect(page).toHaveURL("/dashboard");
  await expect(page.getByText(/dashboard/i)).toBeVisible();
});

test("unauthenticated user is redirected to login", async ({ page }) => {
  await page.goto("/dashboard");
  await expect(page).toHaveURL("/login");
});
```

---

### ✅ Example: Using Fixtures for Isolation

```ts
import { expect } from "@playwright/test";
import { test } from "../fixtures/auth.fixture";

test("admin can access dashboard", async ({ page, loginAsAdmin }) => {
  await loginAsAdmin(); // fixture ensures fresh login
  await page.goto("/dashboard");
  await expect(page.getByText(/dashboard/i)).toBeVisible();
});

test("editor cannot access admin page", async ({ page, loginAsEditor }) => {
  await loginAsEditor();
  await page.goto("/admin");
  await expect(page).toHaveURL("/403");
});
```

---

### ✅ Example: Parallel Execution

```bash
# Run tests in parallel with 4 workers
npx playwright test --workers=4

# Run tests in random order (to catch hidden dependencies)
npx playwright test --shard=2/4
```

---

### 🔑 Checklist for Test Independence

- [ ] Each test sets up its own state.  
- [ ] No test depends on another test’s outcome.  
- [ ] Tests can run in parallel without conflicts.  
- [ ] Tests can run in random order.  
- [ ] Shared state (DB, storage) is reset or isolated.  
- [ ] Fixtures are used for common setup (auth, data).  

---

## 9. Test Data Management

E2E tests must use **isolated, predictable, and disposable data**.  
This ensures tests can run in **parallel** and **independently** without conflicts.

---

### ✅ Rules

- Use **unique test data** per test (e.g., random usernames, IDs).
- Never rely on **shared mutable data** between tests.
- If tests require persistent data (e.g., admin user), seed it once in the test environment.
- Clean up test data after use (if it pollutes shared state).
- Prefer **API setup** (seeding via backend API) over UI setup for speed.
- Use **fixtures** to prepare and tear down test data.
- Use **namespaced identifiers** (e.g., `test_user_1234`) to avoid collisions.

---

### ✅ Example: Randomized Test Data

```ts
import { test, expect } from "@playwright/test";

function randomUsername(prefix = "user") {
  return `${prefix}_${Math.floor(Math.random() * 100000)}`;
}

test("user can register with unique username", async ({ page }) => {
  const username = randomUsername();

  await page.goto("/register");
  await page.getByPlaceholder("Username").fill(username);
  await page.getByPlaceholder("Password").fill("password123");
  await page.getByRole("button", { name: /register/i }).click();

  await expect(page.getByText(/welcome/i)).toBeVisible();
});
```

---

### ✅ Example: API Setup Fixture

```ts
// e2e/fixtures/user.fixture.ts
import { test as base } from "@playwright/test";

export const test = base.extend<{
  createTestUser: (role?: string) => Promise<{ username: string }>;
}>({
  createTestUser: async ({ request }, use) => {
    async function createTestUser(role = "user") {
      const username = `test_${role}_${Date.now()}`;
      await request.post("/api/test-utils/create-user", {
        data: { username, password: "password123", role },
      });
      return { username };
    }
    await use(createTestUser);
  },
});
```

```ts
// e2e/specs/auth.spec.ts
import { expect } from "@playwright/test";
import { test } from "../fixtures/user.fixture";

test("new user can login", async ({ page, createTestUser }) => {
  const { username } = await createTestUser("user");

  await page.goto("/login");
  await page.getByPlaceholder("Username").fill(username);
  await page.getByPlaceholder("Password").fill("password123");
  await page.getByRole("button", { name: /login/i }).click();

  await expect(page).toHaveURL("/dashboard");
});
```

---

### ✅ Example: Cleanup After Test

```ts
test("delete user after test", async ({ request }) => {
  const username = `cleanup_user_${Date.now()}`;

  // Create user
  await request.post("/api/test-utils/create-user", {
    data: { username, password: "password123" },
  });

  // Delete user after test
  await request.delete(`/api/test-utils/delete-user/${username}`);
});
```

---

### ✅ Example: Namespaced Identifiers

```ts
const testRunId = Date.now();

test("create unique project", async ({ page }) => {
  const projectName = `project_${testRunId}`;
  await page.goto("/projects/new");
  await page.getByPlaceholder("Project Name").fill(projectName);
  await page.getByRole("button", { name: /create/i }).click();
  await expect(page.getByText(projectName)).toBeVisible();
});
```

---

### 🔑 Checklist for Test Data

- [ ] Each test uses **unique data** (randomized or namespaced).  
- [ ] No shared mutable state between tests.  
- [ ] Persistent data (e.g., admin user) is **seeded once** in the environment.  
- [ ] Test data is **cleaned up** if it pollutes shared state.  
- [ ] Prefer **API setup** over UI setup for speed.  
- [ ] Use **fixtures** for reusable data setup/teardown.  

---

## 10. Selectors & Element Identification

Tests should identify elements in the same way a **real user** would interact
with them. This improves readability, accessibility, and reduces maintenance.

---

### ✅ Rules

- Prefer **semantic queries** (`getByRole`, `getByLabel`, `getByText`) over `data-testid`.
- Use **accessible roles** (`button`, `link`, `textbox`, `heading`, etc.).
- Use **labels** (`aria-label`, `<label for="...">`) for form fields.
- Use **text content** for static elements (e.g., headings, paragraphs).
- Use `data-testid` **only when no semantic or accessible selector exists**.
- Never use brittle selectors:
  - ❌ CSS classes (`.btn-primary`)
  - ❌ nth-child selectors
  - ❌ Deep DOM paths

---

### ✅ Preferred Selector Order

1. `getByRole` (best for buttons, links, inputs, headings, etc.)
2. `getByLabel` (best for form fields)
3. `getByPlaceholder` (for inputs with placeholders)
4. `getByText` (for static text content)
5. `getByTestId` (only as a last resort)

---

### ✅ Example: Good Selectors

```ts
// Button by role + name
await page.getByRole("button", { name: /login/i }).click();

// Input by label
await page.getByLabel("Username").fill("admin");

// Input by placeholder
await page.getByPlaceholder("Search...").fill("Playwright");

// Heading by role
await expect(page.getByRole("heading", { name: /dashboard/i })).toBeVisible();
```

---

### ❌ Example: Bad Selectors

```ts
// ❌ Brittle: depends on CSS class
await page.locator(".btn-primary").click();

// ❌ Brittle: depends on DOM structure
await page.locator("div > button:nth-child(2)").click();

// ❌ Overuse of testid
await page.getByTestId("login-button").click();
```

---

### ✅ Example: When to Use data-testid

```tsx
// Component with no accessible role or label
<div data-testid="loading-spinner" className="spinner"></div>
```

```ts
// Test
await expect(page.getByTestId("loading-spinner")).toBeVisible();
```

---

### 🔑 Checklist for Selectors

- [ ] Use **roles** for interactive elements.  
- [ ] Use **labels** for form fields.  
- [ ] Use **placeholders** if no label exists.  
- [ ] Use **text** for static content.  
- [ ] Use `data-testid` **only as a last resort**.  
- [ ] Never use CSS classes, nth-child, or deep DOM paths.  

---
