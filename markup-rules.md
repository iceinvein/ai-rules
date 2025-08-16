## 1. Text Hierarchy & SEO

Headings and text must follow a **logical hierarchy** for accessibility and SEO.  
Screen readers and search engines rely on this structure to understand the page.

---

### ✅ Rules

- Only **one `<h1>` per page** (main title).
- Use `<h2>` for top-level sections, `<h3>` for subsections, etc.
- Do not skip heading levels (e.g., `<h1>` → `<h3>` without `<h2>`).
- Use `<p>` for paragraphs of text, not for headings or labels.
- Use `<ul>`/`<ol>` + `<li>` for lists, not multiple `<p>` tags.
- Use `<section>` or `<article>` to group related content with a heading.
- Ensure headings are **descriptive** (not “Section 1” or “Click here”).

---

### ✅ Example: Good Text Hierarchy

```tsx
<main>
  <h1>Dashboard</h1>
  <section>
    <h2>Recent Activity</h2>
    <p>Welcome back, Dik! Here’s what’s new:</p>
    <ul>
      <li>42 new followers</li>
      <li>10 new posts</li>
    </ul>
  </section>
  <section>
    <h2>Account Settings</h2>
    <h3>Profile</h3>
    <p>Update your name, email, and password.</p>
  </section>
</main>
```

---

### ❌ Example: Bad Text Hierarchy

```tsx
<div className="title">Dashboard</div>
<p>Recent Activity</p>
<p>Welcome back, Dik!</p>
<p>42 new followers</p>
<p>10 new posts</p>
<p>Account Settings</p>
<p>Profile</p>
```

👉 Problems:

- No `<h1>`–`<h3>` headings.
- Section titles are `<p>` tags.
- Stats are `<p>` tags instead of a list.

---

### 🔑 Checklist

- [ ] One `<h1>` per page.  
- [ ] Headings follow logical order (`h1` → `h2` → `h3`).  
- [ ] No skipped heading levels.  
- [ ] Section titles use headings, not `<p>`.  
- [ ] Lists use `<ul>`/`<ol>` + `<li>`.  
- [ ] Headings are descriptive and meaningful.  

---

## 20. Markup Hierarchy Rules

HTML must be **valid and properly nested**. Invalid markup breaks accessibility,
causes rendering issues, and confuses tests.

---

### ❌ Common Mistakes

- Nested `<p>` tags:

  ```html
  <p>Another thing <p>Something</p></p>
  ```

- Block elements inside inline elements:

  ```html
  <span><div>Invalid</div></span>
  ```

- Headings inside paragraphs:

  ```html
  <p><h2>Title</h2></p>
  ```

- Multiple `<main>` elements on one page.

---

### ✅ Rules

- `<p>` cannot contain other block elements (only inline text/phrases).
- `<h1>`–`<h6>` cannot be nested inside `<p>`.
- `<ul>`/`<ol>` must only contain `<li>` children.
- `<table>` must contain `<thead>`, `<tbody>`, `<tr>`, `<td>`/`<th>`.
- `<main>` must appear only once per page.
- `<section>`/`<article>` should contain a heading (`<h2>`–`<h6>`).
- Inline elements (`<span>`, `<a>`, `<strong>`, `<em>`) cannot wrap block elements.

---

### ✅ Example: Good Markup

```tsx
<main>
  <h1>Blog</h1>
  <section>
    <h2>Latest Posts</h2>
    <article>
      <h3>Understanding React Hooks</h3>
      <p>React hooks let you use state and lifecycle features in functional components.</p>
    </article>
  </section>
</main>
```

---

### ❌ Example: Bad Markup

```tsx
<main>
  <p><h1>Blog</h1></p>
  <p>Latest Posts</p>
  <p><p>Nested paragraph</p></p>
  <span><div>Invalid nesting</div></span>
</main>
```

---

### 🔑 Checklist

- [ ] No nested `<p>` tags.  
- [ ] No block elements inside inline elements.  
- [ ] Headings are not wrapped in `<p>`.  
- [ ] Lists contain only `<li>` children.  
- [ ] Only one `<main>` per page.  
- [ ] `<section>`/`<article>` contain a heading.  
- [ ] Inline elements only wrap inline content.  

---

## 2. Text Rendering Rules

Text must be rendered in a way that is **semantic, accessible, and testable**.  
This ensures screen readers, Playwright tests, and developers can all interpret
the UI consistently.

---

### ✅ Rules

- Use **headings (`<h1>`–`<h6>`)** for titles and section headers.
  - Only one `<h1>` per page (main title).
  - Use `<h2>`–`<h6>` for subsections, in order.
- Use **paragraphs (`<p>`)** only for blocks of prose or descriptive text.
- Use **inline text (`<span>`)** only for styling inline fragments inside other text.
- Do not use `<div>` for text content.
- Use **semantic elements** for special text:
  - `<strong>` or `<b>` for emphasis (not just styling).
  - `<em>` or `<i>` for emphasis/voice (not just styling).
  - `<code>` for inline code snippets.
  - `<blockquote>` for quotes.
- Avoid wrapping single words or labels in `<p>` or `<div>` — use the correct element.
- Ensure text is **accessible**:
  - Buttons and links must have visible text or `aria-label`.
  - Icons must have `<title>` or `aria-hidden="true"` if decorative.

---

### ✅ Example: Good Text Rendering

```tsx
<main>
  <h1>Dashboard</h1>
  <section>
    <h2>Recent Activity</h2>
    <p>
      Welcome back, Dik! Here’s what’s been happening in your account.
    </p>
    <ul>
      <li><strong>42</strong> new followers</li>
      <li><strong>10</strong> new posts</li>
    </ul>
    <button>View More</button>
  </section>
</main>
```

---

### ❌ Example: Bad Text Rendering

```tsx
<div className="title">Dashboard</div>
<div className="subtitle">Recent Activity</div>
<p>Welcome back, Dik!</p>
<p>42 new followers</p>
<p>10 new posts</p>
<div onClick={handleClick}>View More</div>
```

👉 Problems:

- Headings are `<div>`s, not `<h1>`/`<h2>`.
- Stats are `<p>`s instead of a list.
- Button is a `<div>` with `onClick`.

---

### ✅ Playwright Test (Semantic Version)

```ts
await expect(page.getByRole("heading", { name: /dashboard/i })).toBeVisible();
await expect(page.getByRole("heading", { name: /recent activity/i })).toBeVisible();
await expect(page.getByText(/42 new followers/i)).toBeVisible();
await page.getByRole("button", { name: /view more/i }).click();
```

👉 Clean, accessible, and matches how a user perceives the UI.

---

### 🔑 Checklist for Text Rendering

- [ ] Use `<h1>`–`<h6>` for headings (structured, ordered).  
- [ ] Use `<p>` only for paragraphs of text.  
- [ ] Use `<span>` only for inline fragments inside text.  
- [ ] Use `<strong>`, `<em>`, `<code>`, `<blockquote>` where appropriate.  
- [ ] No `<div>` or `<p>` used for headings, labels, or buttons.  
- [ ] All interactive elements (buttons, links) have visible text or `aria-label`.  
- [ ] Icons have `<title>` or `aria-hidden="true"`.  

---

## 3. Common Soup Patterns (Div, Span, P)

Overusing generic elements like `<div>`, `<span>`, and `<p>` creates **soup**:  
markup with no semantic meaning, poor accessibility, and brittle tests.  

---

### ❌ Problems with Soup

- **Accessibility**: Screen readers can’t understand structure.  
- **Testing**: Playwright/RTL must rely on brittle selectors (`.class`, `nth-child`).  
- **Maintainability**: Harder for devs to read and reason about.  
- **SEO**: Search engines can’t parse structure properly.  

---

### 🔄 Soup → Semantic Mapping

| ❌ Soup Pattern                        | ✅ Semantic Alternative                  | Why? |
|----------------------------------------|------------------------------------------|------|
| `<div onClick={...}>`                  | `<button>` or `<a>`                      | Interactive, focusable, keyboard-friendly |
| `<div class="title">`                  | `<h1>`–`<h6>`                            | Headings provide structure & accessibility |
| `<div class="form">`                   | `<form>`                                 | Semantic form container |
| `<div class="label">`                  | `<label>`                                | Associates text with input |
| `<div class="input">`                  | `<input>`, `<textarea>`, `<select>`      | Native form controls |
| `<div class="nav">`                    | `<nav>` + `<a>`/`<NavLink>`              | Semantic navigation landmark |
| `<div class="list">` + `<div>` items   | `<ul>`/`<ol>` + `<li>`                   | Proper list semantics |
| `<div class="footer">`                 | `<footer>`                               | Semantic page/footer landmark |
| `<div class="header">`                 | `<header>`                               | Semantic page/header landmark |
| `<div class="section">`                | `<section>` or `<article>`               | Semantic grouping |
| `<div class="stat">Key: Value</div>`   | `<dl><dt>Key</dt><dd>Value</dd></dl>`    | Key-value pairs |
| `<span>` used for styling only         | Use semantic element or CSS pseudo-class | `<span>` should be inline text only |
| `<p>` used as heading                  | `<h1>`–`<h6>`                            | Headings, not paragraphs |
| `<p>` used as label                    | `<label>`                                | Labels, not paragraphs |
| `<p>` used as button/link              | `<button>` or `<a>`                      | Interactive elements |
| `<p>` used for list items              | `<ul>`/`<ol>` + `<li>`                   | Proper list semantics |

---

### ✅ Example: Bad (Soup)

```tsx
<div className="card">
  <p className="title">User Stats</p>
  <p>Posts: 42</p>
  <p>Followers: 100</p>
  <p onClick={handleRefresh} className="btn">Refresh</p>
</div>
```

---

### ✅ Example: Good (Semantic)

```tsx
<section className="p-4 border rounded shadow">
  <h2>User Stats</h2>
  <dl>
    <div>
      <dt>Posts</dt>
      <dd>42</dd>
    </div>
    <div>
      <dt>Followers</dt>
      <dd>100</dd>
    </div>
  </dl>
  <button onClick={handleRefresh}>Refresh</button>
</section>
```

---

### ✅ Playwright Test (Semantic Version)

```ts
await expect(page.getByRole("heading", { name: /user stats/i })).toBeVisible();
await expect(page.getByText(/posts/i)).toBeVisible();
await page.getByRole("button", { name: /refresh/i }).click();
```

👉 No brittle selectors, no `data-testid` needed.  

---

### 🔑 Checklist

- [ ] No `<div>` or `<span>` used for interactive elements.  
- [ ] `<p>` only used for **paragraphs of text**.  
- [ ] Headings use `<h1>`–`<h6>`.  
- [ ] Forms use `<form>`, `<label>`, `<input>`.  
- [ ] Navigation uses `<nav>` + `<a>`/`<NavLink>`.  
- [ ] Lists use `<ul>`/`<ol>` + `<li>`.  
- [ ] Key-value data uses `<dl>` + `<dt>` + `<dd>`.  
- [ ] Layout uses `<header>`, `<main>`, `<section>`, `<footer>`.  
- [ ] `<div>`/`<span>` only used when no semantic element fits.  

---

## 4. Avoiding "Div Soup"

**"Div soup"** happens when developers overuse `<div>` and `<span>` for everything,
instead of using semantic HTML elements.  

This leads to:

- ❌ Poor accessibility (screen readers can’t understand structure).
- ❌ Hard-to-test components (Playwright/RTL must rely on brittle selectors).
- ❌ Reduced SEO (search engines can’t parse structure).
- ❌ Harder-to-maintain code (no clear meaning in markup).

---

### ✅ Rules

- Always use the **semantic element** that matches the purpose.
- Only use `<div>` or `<span>` when no semantic element fits.
- Never use `<div>` with `onClick` → use `<button>` or `<a>`.
- Ensure headings, forms, navigation, and lists use proper HTML tags.

---

### 🍲 Example: Div Soup (Bad)

```tsx
<div className="card">
  <div className="card-title">User Stats</div>
  <div className="card-content">
    <div className="stat">Posts: 42</div>
    <div className="stat">Followers: 100</div>
  </div>
  <div className="card-actions">
    <div className="button" onClick={handleRefresh}>Refresh</div>
  </div>
</div>
```

#### ❌ Playwright Test (Brittle)

```ts
// Relies on CSS class or nth-child
await page.locator(".button").click();
await expect(page.locator(".card-title")).toHaveText("User Stats");
```

---

### ✅ Example: Semantic (Good)

```tsx
<section className="p-4 border rounded shadow">
  <h2 className="text-lg font-bold">User Stats</h2>
  <dl className="mt-2">
    <div>
      <dt className="font-medium">Posts</dt>
      <dd>42</dd>
    </div>
    <div>
      <dt className="font-medium">Followers</dt>
      <dd>100</dd>
    </div>
  </dl>
  <button
    onClick={handleRefresh}
    className="mt-4 px-3 py-1 bg-blue-500 text-white rounded"
  >
    Refresh
  </button>
</section>
```

#### ✅ Playwright Test (Stable & Accessible)

```ts
// Query by role and accessible name
await page.getByRole("button", { name: /refresh/i }).click();
await expect(page.getByRole("heading", { name: /user stats/i })).toBeVisible();
await expect(page.getByText(/posts/i)).toBeVisible();
await expect(page.getByText(/followers/i)).toBeVisible();
```

👉 Benefits:

- No reliance on CSS classes or DOM structure.  
- Queries match how a **real user** interacts (button, heading, text).  
- Tests are **more stable** and **self-documenting**.  

---

### 🔄 Div Soup → Semantic Refactor Mapping

| ❌ Div Soup Pattern                  | ✅ Semantic Alternative         |
|--------------------------------------|---------------------------------|
| `<div onClick={...}>`                | `<button>` or `<a>`             |
| `<div class="title">`                | `<h1>`–`<h6>`                   |
| `<div class="form">`                 | `<form>`                        |
| `<div class="label">`                | `<label>`                       |
| `<div class="input">`                | `<input>`, `<textarea>`, etc.   |
| `<div class="nav">`                  | `<nav>`                         |
| `<div class="list">` + `<div>` items | `<ul>` + `<li>`                 |
| `<div class="footer">`               | `<footer>`                      |
| `<div class="header">`               | `<header>`                      |
| `<div class="section">`              | `<section>` or `<article>`      |
| `<div class="stat">Key: Value</div>` | `<dl><dt>Key</dt><dd>Value</dd></dl>` |

---

### 🔑 Checklist

- [ ] No `<div>` or `<span>` used for buttons, links, or inputs.  
- [ ] Headings use `<h1>`–`<h6>`.  
- [ ] Forms use `<form>`, `<label>`, `<input>`.  
- [ ] Navigation uses `<nav>` + `<a>`/`<NavLink>`.  
- [ ] Lists use `<ul>`/`<ol>` + `<li>`.  
- [ ] Key-value data uses `<dl>` + `<dt>` + `<dd>`.  
- [ ] Layout uses `<header>`, `<main>`, `<section>`, `<footer>`.  
- [ ] `<div>`/`<span>` only used when no semantic element fits.  
- [ ] Playwright tests should query by **role/label/text**, not CSS classes.  

---

## 5. Markup Hierarchy Rules

HTML must be **valid and properly nested**. Invalid markup breaks accessibility,
causes rendering issues, and confuses tests.

---

### ❌ Common Mistakes

- Nested `<p>` tags:

  ```html
  <p>Another thing <p>Something</p></p>
  ```

- Block elements inside inline elements:

  ```html
  <span><div>Invalid</div></span>
  ```

- Headings inside paragraphs:

  ```html
  <p><h2>Title</h2></p>
  ```

- Multiple `<main>` elements on one page.

---

### ✅ Rules

- `<p>` cannot contain other block elements (only inline text/phrases).
- `<h1>`–`<h6>` cannot be nested inside `<p>`.
- `<ul>`/`<ol>` must only contain `<li>` children.
- `<table>` must contain `<thead>`, `<tbody>`, `<tr>`, `<td>`/`<th>`.
- `<main>` must appear only once per page.
- `<section>`/`<article>` should contain a heading (`<h2>`–`<h6>`).
- Inline elements (`<span>`, `<a>`, `<strong>`, `<em>`) cannot wrap block elements.

---

### 🔄 Common Invalid Markup → Fix Mapping

| ❌ Invalid Markup Example                          | ✅ Correct Fix                                                                 |
|---------------------------------------------------|--------------------------------------------------------------------------------|
| `<p><p>Nested paragraph</p></p>`                  | `<p>First paragraph</p><p>Second paragraph</p>`                                |
| `<p><h2>Title</h2></p>`                           | `<h2>Title</h2><p>Supporting text</p>`                                         |
| `<span><div>Invalid</div></span>`                 | `<div><span>Valid inline text</span></div>`                                    |
| `<ul><div>Item</div></ul>`                        | `<ul><li>Item</li></ul>`                                                       |
| `<table><div>Row</div></table>`                   | `<table><tbody><tr><td>Row</td></tr></tbody></table>`                          |
| `<main><main>Nested main</main></main>`           | `<main>Content</main>` (only one `<main>` per page)                            |
| `<section>Content without heading</section>`      | `<section><h2>Section Title</h2><p>Content</p></section>`                      |
| `<a><div>Clickable block</div></a>`               | `<a href="/path">Clickable block</a>` (or use `<button>` if it’s an action)    |
| `<label><div>Username</div></label>`              | `<label for="username">Username</label><input id="username" />`                |
| `<p><ul><li>Item</li></ul></p>`                   | `<p>Intro text</p><ul><li>Item</li></ul>`                                      |

---

### ✅ Example: Good Markup

```tsx
<main>
  <h1>Blog</h1>
  <section>
    <h2>Latest Posts</h2>
    <article>
      <h3>Understanding React Hooks</h3>
      <p>React hooks let you use state and lifecycle features in functional components.</p>
    </article>
  </section>
</main>
```

---

### ❌ Example: Bad Markup

```tsx
<main>
  <p><h1>Blog</h1></p>
  <p>Latest Posts</p>
  <p><p>Nested paragraph</p></p>
  <span><div>Invalid nesting</div></span>
</main>
```

---

### 🔑 Checklist

- [ ] No nested `<p>` tags.  
- [ ] No block elements inside inline elements.  
- [ ] Headings are not wrapped in `<p>`.  
- [ ] Lists contain only `<li>` children.  
- [ ] Only one `<main>` per page.  
- [ ] `<section>`/`<article>` contain a heading.  
- [ ] Inline elements only wrap inline content.  
- [ ] Use the mapping table above to refactor invalid markup.  

---
