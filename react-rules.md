# 🧩 React AI Agent Rules

This document defines the rules and best practices for generating React code
using **Nano Stores**, **React Query**, **TailwindCSS**, **Vite**, and **Vitest**.  
All AI-generated code must follow these guidelines.

---

## 1. Component Structure

- Use **functional components** with hooks.
- Keep components **small, reusable, and focused**.
- Use **TypeScript** for type safety.

---

## 2. State Management

- Use **Nano Stores** for global state (lightweight, reactive).
- Use `useState` or `useReducer` for local component state.
- Avoid prop drilling by leveraging Nano Stores or composition.

---

## 3. Data Fetching & Server State

- Use **React Query** for all async data fetching, caching, and mutations.
- Always handle **loading** and **error** states.
- Prefer `useQuery` for reads and `useMutation` for writes.

---

## 4. Side Effects

- Use `useEffect` only for **non-data-fetching side effects**
  (subscriptions, DOM events).
- Always **clean up** effects.

---

## 5. Styling

- Use **TailwindCSS** for styling.
- Keep class names **semantic and composable**.
- Avoid inline styles unless dynamic.

---

## 6. Rendering & Performance

- Use `React.memo` for expensive components.
- Use `useCallback` and `useMemo` when passing functions/objects to children.
- Avoid unnecessary re-renders by structuring state properly.

---

## 7. Error Handling

- Use **Error Boundaries** for render errors.
- Handle async errors gracefully with React Query’s `onError` or error states.

---

## 8. Testing

- Use **Vitest** as the test runner (Vite-native).
- Use **React Testing Library** for rendering and assertions.
- Test **behavior, not implementation details**.
- Use **mocks** for hooks, libraries, and APIs (see Mocking Guide below).

---

## 9. Accessibility

Accessibility (a11y) must always be considered. Components should be usable by
**screen readers, keyboard navigation, and users with visual impairments**.

### ✅ Rules

- Always add `alt` text for images.
- Use **semantic HTML** (`<button>`, `<a>`, `<nav>`, `<header>`, etc.).
- Do **not** use non-interactive elements (`<div>`, `<span>`) with `onClick`.
  - Use `<button>` for actions.
  - Use `<a>` for navigation.
- Ensure **keyboard accessibility**:
  - All interactive elements must be focusable (`tabIndex=0` if custom).
  - Provide visible focus states (Tailwind: `focus:outline-none focus:ring-2`).
- Ensure **color contrast** meets WCAG AA (use Tailwind’s accessible palette).
- For **SVG icons**:
  - Add a `<title>` inside the `<svg>` for screen readers.
  - Use `role="img"` and `aria-hidden="true"` if decorative.
- Use **ARIA attributes** when semantic HTML is not enough.
- Do not rely on **color alone** to convey meaning (use text/icons as well).

---

### ✅ Example: Accessible Button

```tsx
// ❌ Bad: div with onClick
<div onClick={handleClick} className="p-2 bg-blue-500 text-white">
  Click Me
</div>

// ✅ Good: button with proper semantics
<button
  onClick={handleClick}
  className="p-2 bg-blue-500 text-white rounded focus:outline-none focus:ring-2 focus:ring-blue-300"
>
  Click Me
</button>
```

---

### ✅ Example: Accessible SVG

```tsx
// Accessible icon with title
<svg
  xmlns="http://www.w3.org/2000/svg"
  viewBox="0 0 20 20"
  fill="currentColor"
  role="img"
  aria-labelledby="iconTitle"
  className="w-6 h-6"
>
  <title id="iconTitle">Search Icon</title>
  <path d="M..." />
</svg>

// Decorative icon (hidden from screen readers)
<svg
  xmlns="http://www.w3.org/2000/svg"
  viewBox="0 0 20 20"
  fill="currentColor"
  aria-hidden="true"
  className="w-6 h-6"
>
  <path d="M..." />
</svg>
```

---

### ✅ Example: Keyboard Navigation

```tsx
// Custom interactive element (must be focusable + keyboard accessible)
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => e.key === "Enter" && handleClick()}
  className="p-2 bg-green-500 text-white rounded focus:outline-none focus:ring-2 focus:ring-green-300"
>
  Press Enter or Click
</div>
```

---

### ✅ Example: Color Contrast

```tsx
// ❌ Bad: low contrast (gray on gray)
<p className="text-gray-400 bg-gray-200">Low contrast text</p>

// ✅ Good: accessible contrast
<p className="text-gray-800 bg-gray-100">High contrast text</p>
```

---

### ✅ Example: ARIA for Dynamic Content

```tsx
// Live region for async updates
<p role="status" aria-live="polite">
  Loading users...
</p>
```

---

### 🔑 Accessibility Checklist

- [ ] Images have `alt` text.  
- [ ] SVGs have `<title>` or `aria-hidden="true"`.  
- [ ] No `onClick` on non-interactive elements (`div`, `span`).  
- [ ] All interactive elements are **focusable** and **keyboard accessible**.  
- [ ] Visible **focus states** are present.  
- [ ] Color contrast meets WCAG AA.  
- [ ] ARIA attributes used where needed.  
- [ ] No reliance on color alone for meaning.

---

## 10. Composition (When & How to Use It)

Composition is preferred over prop drilling and rigid props when building
flexible, reusable components.

### ✅ When to Use Composition

- When a component should be **flexible** (not tied to fixed props).
- When you want to **avoid prop drilling**.
- When building **layouts, modals, cards, or wrappers**.
- When you want **slots** (header, footer, actions).
- When you want to **separate concerns** (container vs. content).

(See examples in previous section.)

---

## 11. Mocking Guide for React Testing (Vitest)

When testing React components, **mock external dependencies** (hooks, stores,
libraries) to isolate behavior.

### 11.1 Vitest Setup

- Add a `setupTests.ts` file in `src/` or `tests/`.
- Configure it in `vite.config.ts`:

```ts
// vite.config.ts
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: "jsdom",
    setupFiles: "./src/setupTests.ts",
  },
});
```

- Example `setupTests.ts`:

```ts
import "@testing-library/jest-dom";
```

---

### 11.2 Mocking React Query

```tsx
import { render, screen } from "@testing-library/react";
import { vi } from "vitest";
import UserList from "./UserList";

vi.mock("@tanstack/react-query", () => ({
  useQuery: vi.fn().mockReturnValue({
    data: [{ id: 1, name: "Mock User" }],
    isLoading: false,
    error: null,
  }),
}));

test("renders mocked users", () => {
  render(<UserList />);
  expect(screen.getByText("Mock User")).toBeInTheDocument();
});
```

---

### 11.3 Mocking Nano Stores

```tsx
import { render, screen } from "@testing-library/react";
import { vi } from "vitest";
import { useStore } from "@nanostores/react";
import UserList from "./UserList";

vi.mock("@nanostores/react", async (importOriginal) => {
  const actual = await importOriginal();
  return {
    ...actual,
    useStore: vi.fn().mockImplementation(() => 1), // always return userId=1
  };
});

test("highlights selected user", () => {
  render(<UserList />);
  expect(screen.getByText("Mock User")).toHaveClass("bg-blue-500");
});
```

---

### 11.4 Mocking Custom Hooks

```tsx
vi.mock("../hooks/useAuth", () => ({
  useAuth: () => ({ user: { name: "Test User" }, login: vi.fn() }),
}));
```

---

### 11.5 Mocking Fetch / API Calls

Use `vi.fn()` or **MSW (Mock Service Worker)** for realistic API mocks.

```tsx
global.fetch = vi.fn().mockResolvedValue({
  ok: true,
  json: async () => [{ id: 1, name: "Mocked API User" }],
});
```

---

## ✅ Do / ❌ Don’t Checklist

### ✅ Do

- Use **functional components** with hooks.
- Use **Nano Stores** for global state.
- Use **React Query** for server state.
- Use **TailwindCSS** for styling.
- Use **composition** instead of prop drilling.
- Always handle **loading** and **error** states.
- Write **tests with Vitest + React Testing Library**.
- Mock **hooks, stores, and APIs** in tests.
- Ensure **accessibility** (alt text, ARIA, keyboard nav).

### ❌ Don’t

- ❌ Use class components (unless legacy).
- ❌ Mutate props or state directly.
- ❌ Fetch data inside `useEffect` without React Query.
- ❌ Hardcode styles inline (except dynamic cases).
- ❌ Overuse context for global state (prefer Nano Stores).
- ❌ Test implementation details (test behavior instead).
- ❌ Ignore cleanup in `useEffect`.
- ❌ Skip error handling.

---

## 🔑 Summary

- **Nano Stores** → global state  
- **React Query** → server state  
- **TailwindCSS** → styling  
- **Functional components + hooks** → structure  
- **Composition** → flexibility & reusability  
- **TypeScript** → type safety  
- **Vitest + RTL** → testing  
- **Mocking** → isolate hooks, stores, APIs  
- **Accessibility + performance** → always considered  

---

## 12. Routing (React Router v7 Declarative)

We use **React Router v7** with the **declarative route configuration API**.  
All routes must be defined in a central `routes.tsx` (or `routes.ts`) file and
loaded via `createBrowserRouter`.

---

### ✅ Rules

- Use **`createBrowserRouter`** with a **route config object** (not JSX `<Routes>`).
- Define routes in a **single `routes.tsx` file** for consistency.
- Use **nested routes** for layouts (e.g., dashboard, auth).
- Use **`loader`** for data fetching and **`action`** for mutations.
- Use **`errorElement`** for error boundaries at route level.
- Use **`lazy`** for code-splitting routes.
- Use **`<Link>`** or `<NavLink>` for navigation (never `a href`).
- Use **`useNavigate`** for programmatic navigation.
- Use **`useParams`, `useLoaderData`, `useActionData`** for route data.
- Keep routes **typed** with TypeScript.
- Do not hardcode paths in multiple places — use a **`paths.ts` constants file**.

---

### ✅ Protected Routes & Auth Routing

Authentication is handled via **Nano Stores** for global state and **React Router
loaders** for route protection.

#### ✅ Rules

- Store auth state in a **Nano Store** (`authStore`).
- Use a **loader** to check authentication before rendering protected routes.
- Redirect unauthenticated users to `/login`.
- Use `paths.ts` constants for redirects.
- Keep auth logic centralized (no scattered checks in components).

---

### ✅ Example: authStore.ts

```ts
import { atom } from "nanostores";

export type AuthUser = { id: number; name: string } | null;

export const authStore = atom<AuthUser>(null);

export function login(user: AuthUser) {
  authStore.set(user);
}

export function logout() {
  authStore.set(null);
}
```

---

### ✅ Example: Protected Loader

```ts
import { redirect } from "react-router-dom";
import { authStore } from "./stores/authStore";
import { paths } from "./paths";

export async function requireAuthLoader() {
  const user = authStore.get();
  if (!user) {
    throw redirect(paths.login);
  }
  return user;
}
```

---

### ✅ Example: routes.tsx

```tsx
import { createBrowserRouter } from "react-router-dom";
import AppLayout from "./layouts/AppLayout";
import ErrorPage from "./pages/ErrorPage";
import HomePage from "./pages/HomePage";
import DashboardPage from "./pages/DashboardPage";
import LoginPage, { action as loginAction } from "./pages/LoginPage";
import { requireAuthLoader } from "./authLoader";
import { paths } from "./paths";

export const router = createBrowserRouter([
  {
    path: paths.home,
    element: <AppLayout />,
    errorElement: <ErrorPage />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      {
        path: paths.dashboard,
        element: <DashboardPage />,
        loader: requireAuthLoader, // 🔒 protected
      },
      {
        path: paths.login,
        element: <LoginPage />,
        action: loginAction,
      },
    ],
  },
]);
```

---

### ✅ Example: LoginPage with Action

```tsx
import { Form, useActionData, redirect } from "react-router-dom";
import { login } from "../stores/authStore";
import { paths } from "../paths";

export async function action({ request }: { request: Request }) {
  const formData = await request.formData();
  const username = formData.get("username");

  if (username === "admin") {
    login({ id: 1, name: "Admin" });
    return redirect(paths.dashboard);
  }

  return { error: "Invalid credentials" };
}

export default function LoginPage() {
  const data = useActionData() as { error?: string };
  return (
    <Form method="post" className="flex flex-col gap-2">
      <input
        name="username"
        placeholder="Username"
        className="border p-2 rounded"
      />
      <button
        type="submit"
        className="bg-blue-500 text-white px-3 py-1 rounded"
      >
        Login
      </button>
      {data?.error && <p className="text-red-500">{data.error}</p>}
    </Form>
  );
}
```

---

### ✅ Example: paths.ts

```ts
export const paths = {
  home: "/",
  dashboard: "/dashboard",
  login: "/login",
};
```

---

### ❌ Don’t

- ❌ Check auth state inside components (use loaders instead).
- ❌ Use `useEffect` for redirecting unauthenticated users.
- ❌ Hardcode `/login` or `/dashboard` in multiple places (use `paths.ts`).
- ❌ Skip `errorElement` for protected routes.

---

## 13. Role-Based Routing (Authorization)

In addition to authentication, some routes require **specific roles** (e.g.,
`admin`, `editor`, `user`). We enforce this using **Nano Stores** for auth state
and **React Router loaders** for role checks.

---

### ✅ Rules

- Store the user’s **role(s)** in the `authStore`.
- Use a **role-based loader** to check if the user has the required role(s).
- Redirect unauthorized users to a **403 Forbidden page** or back to `/login`.
- Keep role logic centralized (no scattered role checks in components).
- Use `paths.ts` constants for redirects.

---

### ✅ Example: authStore.ts

```ts
import { atom } from "nanostores";

export type Role = "admin" | "editor" | "user";

export type AuthUser = {
  id: number;
  name: string;
  roles: Role[]; // ✅ multiple roles supported
} | null;

export const authStore = atom<AuthUser>(null);

export function login(user: AuthUser) {
  authStore.set(user);
}

export function logout() {
  authStore.set(null);
}
```

---

### ✅ Example: roleLoader.ts

```ts
import { redirect } from "react-router-dom";
import { authStore, Role } from "./stores/authStore";
import { paths } from "./paths";

// Loader factory for role-based access
export function requireRolesLoader(requiredRoles: Role[]) {
  return async () => {
    const user = authStore.get();
    if (!user) {
      throw redirect(paths.login);
    }
    const hasRole = user.roles.some((role) => requiredRoles.includes(role));
    if (!hasRole) {
      throw redirect(paths.forbidden); // 403 page
    }
    return user;
  };
}
```

---

### ✅ Example: routes.tsx

```tsx
import { createBrowserRouter } from "react-router-dom";
import AppLayout from "./layouts/AppLayout";
import ErrorPage from "./pages/ErrorPage";
import HomePage from "./pages/HomePage";
import DashboardPage from "./pages/DashboardPage";
import AdminPage from "./pages/AdminPage";
import EditorPage from "./pages/EditorPage";
import ForbiddenPage from "./pages/ForbiddenPage";
import LoginPage, { action as loginAction } from "./pages/LoginPage";
import { requireAuthLoader } from "./authLoader";
import { requireRolesLoader } from "./roleLoader";
import { paths } from "./paths";

export const router = createBrowserRouter([
  {
    path: paths.home,
    element: <AppLayout />,
    errorElement: <ErrorPage />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      {
        path: paths.dashboard,
        element: <DashboardPage />,
        loader: requireAuthLoader, // 🔒 any logged-in user
      },
      {
        path: paths.admin,
        element: <AdminPage />,
        loader: requireRolesLoader(["admin"]), // 🔒 only admins
      },
      {
        path: paths.editor,
        element: <EditorPage />,
        loader: requireRolesLoader(["admin", "editor"]), // 🔒 admins OR editors
      },
      {
        path: paths.login,
        element: <LoginPage />,
        action: loginAction,
      },
      {
        path: paths.forbidden,
        element: <ForbiddenPage />,
      },
    ],
  },
]);
```

---

### ✅ Example: paths.ts

```ts
export const paths = {
  home: "/",
  dashboard: "/dashboard",
  admin: "/admin",
  editor: "/editor",
  login: "/login",
  forbidden: "/403",
};
```

---

### ✅ Example: ForbiddenPage.tsx

```tsx
export default function ForbiddenPage() {
  return (
    <div className="flex flex-col items-center justify-center h-screen">
      <h1 className="text-3xl font-bold text-red-600">403 Forbidden</h1>
      <p className="text-gray-600 mt-2">
        You do not have permission to access this page.
      </p>
    </div>
  );
}
```

---

### ❌ Don’t

- ❌ Check roles inside components (use loaders instead).
- ❌ Hardcode role checks in multiple places.
- ❌ Redirect unauthorized users manually with `useEffect`.
- ❌ Skip a `403 Forbidden` page (always provide feedback).

---

## 14. Testing Route Guards (Vitest + React Testing Library)

Protected and role-based routes must be tested to ensure correct behavior for
authenticated, unauthenticated, and unauthorized users.

We use **Vitest** + **React Testing Library** with **mocked Nano Stores**.

---

### ✅ Rules

- Always test **3 cases** for protected routes:
  1. Unauthenticated → redirected to `/login`.
  2. Authenticated → allowed access.
  3. Unauthorized role → redirected to `/403`.
- Mock `authStore` values in tests.
- Use `MemoryRouter` with `RouterProvider` for isolated route testing.
- Use `screen` queries to assert rendered content or redirects.

---

### ✅ Example: Protected Route Test

```tsx
import { render, screen, waitFor } from "@testing-library/react";
import { RouterProvider, createMemoryRouter } from "react-router-dom";
import { vi } from "vitest";
import { authStore } from "../stores/authStore";
import { router as appRouter } from "../routes";
import { paths } from "../paths";

// Helper to render with memory router
function renderWithRouter(initialPath: string) {
  const router = createMemoryRouter(appRouter.routes, {
    initialEntries: [initialPath],
  });
  return render(<RouterProvider router={router} />);
}

describe("Protected Routes", () => {
  afterEach(() => {
    authStore.set(null); // reset auth
  });

  test("redirects unauthenticated user to login", async () => {
    renderWithRouter(paths.dashboard);
    await waitFor(() => {
      expect(screen.getByText(/login/i)).toBeInTheDocument();
    });
  });

  test("allows authenticated user to access dashboard", async () => {
    authStore.set({ id: 1, name: "Test User", roles: ["user"] });
    renderWithRouter(paths.dashboard);
    await waitFor(() => {
      expect(screen.getByText(/dashboard/i)).toBeInTheDocument();
    });
  });
});
```

---

### ✅ Example: Role-Based Route Test

```tsx
import { render, screen, waitFor } from "@testing-library/react";
import { RouterProvider, createMemoryRouter } from "react-router-dom";
import { authStore } from "../stores/authStore";
import { router as appRouter } from "../routes";
import { paths } from "../paths";

function renderWithRouter(initialPath: string) {
  const router = createMemoryRouter(appRouter.routes, {
    initialEntries: [initialPath],
  });
  return render(<RouterProvider router={router} />);
}

describe("Role-Based Routes", () => {
  afterEach(() => {
    authStore.set(null);
  });

  test("redirects non-admin to forbidden page", async () => {
    authStore.set({ id: 2, name: "Editor", roles: ["editor"] });
    renderWithRouter(paths.admin);
    await waitFor(() => {
      expect(screen.getByText(/403 forbidden/i)).toBeInTheDocument();
    });
  });

  test("allows admin to access admin page", async () => {
    authStore.set({ id: 1, name: "Admin", roles: ["admin"] });
    renderWithRouter(paths.admin);
    await waitFor(() => {
      expect(screen.getByText(/admin page/i)).toBeInTheDocument();
    });
  });

  test("allows editor to access editor page", async () => {
    authStore.set({ id: 3, name: "Editor", roles: ["editor"] });
    renderWithRouter(paths.editor);
    await waitFor(() => {
      expect(screen.getByText(/editor page/i)).toBeInTheDocument();
    });
  });
});
```

---

### ✅ Example: Forbidden Page Test

```tsx
import { render, screen } from "@testing-library/react";
import ForbiddenPage from "../pages/ForbiddenPage";

test("renders forbidden message", () => {
  render(<ForbiddenPage />);
  expect(screen.getByText(/403 forbidden/i)).toBeInTheDocument();
  expect(
    screen.getByText(/you do not have permission/i)
  ).toBeInTheDocument();
});
```

---

### 🔑 Checklist for Route Guard Tests

- [ ] Test unauthenticated → redirected to `/login`.  
- [ ] Test authenticated → access granted.  
- [ ] Test unauthorized role → redirected to `/403`.  
- [ ] Test authorized role → access granted.  
- [ ] Test forbidden page renders correctly.  

---

## 15. Element Identifiability for Testing

React components must be written in a way that makes elements **easy to
identify in tests** without relying on brittle selectors.

---

### ✅ Rules

- Always use **semantic HTML** (`<button>`, `<a>`, `<form>`, `<input>`, `<label>`, `<h1>`).
- Ensure all interactive elements have:
  - A **role** (implicit or explicit).
  - A **label** (text, `aria-label`, or `<label>`).
- Use **descriptive text** for headings, buttons, and links.
- Use `data-testid` **only when no semantic or accessible selector exists**.
- Do not rely on CSS classes or DOM structure for testability.
- Ensure **unique identifiers** for repeated elements (e.g., list items with `key`).

---

### ✅ Example: Good React Code

```tsx
// Button with accessible name
<button onClick={handleLogin}>Login</button>

// Input with label
<label htmlFor="username">Username</label>
<input id="username" name="username" />

// Accessible SVG
<svg role="img" aria-labelledby="searchIconTitle">
  <title id="searchIconTitle">Search</title>
  <path d="..." />
</svg>
```

---

### ❌ Example: Bad React Code

```tsx
// ❌ Non-semantic div with onClick
<div onClick={handleLogin} className="btn">Login</div>

// ❌ Input without label
<input name="username" />

// ❌ Decorative SVG without aria-hidden
<svg><path d="..." /></svg>
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

### 🔑 Checklist for Identifiable Elements

- [ ] Use **semantic HTML** for all interactive elements.  
- [ ] Ensure **labels** for inputs and form fields.  
- [ ] Ensure **accessible names** for buttons and links.  
- [ ] Add `<title>` or `aria-hidden` for SVGs.  
- [ ] Use `data-testid` only as a **last resort**.  
- [ ] Never rely on CSS classes or DOM structure for testability.  

---

## 16. Avoiding "Provider Soup"

When multiple React Context providers are nested directly, it creates
**"Provider Soup"** — deeply indented, hard-to-read, and hard-to-maintain code.

---

### ❌ Example: Provider Soup

```tsx
export default function App() {
  return (
    <ThemeProvider>
      <AuthProvider>
        <SettingsProvider>
          <NotificationsProvider>
            <AnalyticsProvider>
              <AppRoutes />
            </AnalyticsProvider>
          </NotificationsProvider>
        </SettingsProvider>
      </AuthProvider>
    </ThemeProvider>
  );
}
```

👉 Problems:  

- Hard to read and maintain.  
- Difficult to reorder or add/remove providers.  
- Deep indentation makes diffs noisy.  

---

### ✅ Rule: Use `composeProviders`

Create a utility to **compose multiple providers** into a single wrapper.

```tsx
// utils/composeProviders.tsx
import React from "react";

export const composeProviders = (
  providers: React.FC<{ children: React.ReactNode }>[]
) => {
  return ({ children }: { children: React.ReactNode }) =>
    providers.reduceRight(
      (acc, Provider, index) => <Provider key={index}>{acc}</Provider>,
      children
    );
};
```

---

### ✅ Example: Composed Providers

```tsx
// AppProviders.tsx
import {
  ThemeProvider,
  AuthProvider,
  SettingsProvider,
  NotificationsProvider,
  AnalyticsProvider,
} from "./providers";
import { composeProviders } from "./utils/composeProviders";

const AppProviders = composeProviders([
  ThemeProvider,
  AuthProvider,
  SettingsProvider,
  NotificationsProvider,
  AnalyticsProvider,
]);

export default function Providers({ children }: { children: React.ReactNode }) {
  return <AppProviders>{children}</AppProviders>;
}
```

---

### ✅ Usage

```tsx
// App.tsx
import Providers from "./AppProviders";
import AppRoutes from "./AppRoutes";

export default function App() {
  return (
    <Providers>
      <AppRoutes />
    </Providers>
  );
}
```

---

### ✅ Benefits

- Flat, readable provider structure.  
- Easy to add/remove/reorder providers.  
- Centralized provider management.  
- Cleaner diffs in PRs.  

---

### 🔑 Checklist

- [ ] Do not nest providers directly in JSX.  
- [ ] Use `composeProviders` to flatten provider trees.  
- [ ] Group related providers into a single composed provider (e.g., `AppProviders`).  
- [ ] Keep provider composition utilities in `utils/` or `providers/`.  
- [ ] Ensure each composed provider is **named meaningfully**.  

---

## 22. Grouping Providers by Domain

When many providers are needed, avoid creating one giant `AppProviders` array.  
Instead, **group providers by domain** (e.g., UI, Auth, Features) and compose
those groups into the final provider tree.

---

### ✅ Rules

- Group related providers into **domain-specific providers**:
  - `UIProviders` → Theme, Layout, Notifications
  - `AuthProviders` → Authentication, Session, Permissions
  - `FeatureProviders` → Feature flags, A/B testing, Analytics
- Each group should be composed with `composeProviders`.
- The final `AppProviders` should only compose the **domain groups**.
- This keeps provider management modular and easier to maintain.

---

### ✅ Example: Grouped Providers

```tsx
// utils/composeProviders.tsx
import React from "react";

export const composeProviders = (
  providers: React.FC<{ children: React.ReactNode }>[]
) => {
  return ({ children }: { children: React.ReactNode }) =>
    providers.reduceRight(
      (acc, Provider, index) => <Provider key={index}>{acc}</Provider>,
      children
    );
};
```

---

```tsx
// providers/UIProviders.tsx
import { ThemeProvider, NotificationsProvider } from "./ui";
import { composeProviders } from "../utils/composeProviders";

export const UIProviders = composeProviders([
  ThemeProvider,
  NotificationsProvider,
]);
```

---

```tsx
// providers/AuthProviders.tsx
import { AuthProvider, SessionProvider } from "./auth";
import { composeProviders } from "../utils/composeProviders";

export const AuthProviders = composeProviders([
  AuthProvider,
  SessionProvider,
]);
```

---

```tsx
// providers/FeatureProviders.tsx
import { ABTestingProvider, AnalyticsProvider } from "./features";
import { composeProviders } from "../utils/composeProviders";

export const FeatureProviders = composeProviders([
  ABTestingProvider,
  AnalyticsProvider,
]);
```

---

```tsx
// providers/AppProviders.tsx
import { composeProviders } from "../utils/composeProviders";
import { UIProviders } from "./UIProviders";
import { AuthProviders } from "./AuthProviders";
import { FeatureProviders } from "./FeatureProviders";

const AppProviders = composeProviders([
  UIProviders,
  AuthProviders,
  FeatureProviders,
]);

export default function Providers({ children }: { children: React.ReactNode }) {
  return <AppProviders>{children}</AppProviders>;
}
```

---

### ✅ Usage

```tsx
// App.tsx
import Providers from "./providers/AppProviders";
import AppRoutes from "./AppRoutes";

export default function App() {
  return (
    <Providers>
      <AppRoutes />
    </Providers>
  );
}
```

---

### ✅ Benefits

- Modular provider management.  
- Easier to maintain and scale.  
- Clear separation of concerns (UI vs Auth vs Features).  
- Smaller diffs when adding/removing providers.  
- Easier to test domain-specific providers in isolation.  

---

### 🔑 Checklist

- [ ] Do not put all providers in one giant array.  
- [ ] Group providers by **domain** (UI, Auth, Features, etc.).  
- [ ] Use `composeProviders` for each group.  
- [ ] Compose groups into the final `AppProviders`.  
- [ ] Keep provider groups small and focused.  

---
