# 📜 AI Agent Rule: TSDoc Documentation for Functions & Hooks

## Rule

Whenever generating **TypeScript functions** or **React hooks**, the AI agent must
include **TSDoc comments** that describe:

1. **Purpose** – What the function/hook does.  
2. **Parameters** – Each parameter with type and description.  
3. **Returns** – The return type and meaning.  
4. **Usage Example** – A short code snippet showing how to use it.  

---

## ✅ Example for a Function

```ts
/**
 * Adds two numbers together.
 *
 * @param a - The first number to add.
 * @param b - The second number to add.
 * @returns The sum of `a` and `b`.
 *
 * @example
 * ```ts
 * const result = add(2, 3);
 * console.log(result); // 5
 * ```
 */
export function add(a: number, b: number): number {
  return a + b;
}
```

---

## ✅ Example for a Hook

```ts
import { useState } from "react";

/**
 * A custom hook that manages a boolean toggle state.
 *
 * @param initialValue - The initial boolean value (default: false).
 * @returns An array with the current state and a function to toggle it.
 *
 * @example
 * ```ts
 * const [isOpen, toggleIsOpen] = useToggle();
 * toggleIsOpen(); // flips the state
 * ```
 */
export function useToggle(initialValue = false): [boolean, () => void] {
  const [value, setValue] = useState(initialValue);
  const toggle = () => setValue((prev) => !prev);
  return [value, toggle];
}
```

---

## 🔧 Extended TSDoc Best Practices

To ensure consistency and clarity, AI agents must also follow these best practices
when generating TSDoc:

### 1. Type Inference Awareness
- Do not repeat obvious TypeScript types.  
- Instead, explain the **meaning** of the type.  

```ts
/**
 * Calculates the distance between two points in 2D space.
 *
 * @returns The Euclidean distance.
 */
function distance(x1: number, y1: number, x2: number, y2: number): number { ... }
```

---

### 2. Optional & Default Parameters
- Always document when a parameter is optional and its default value.  

```ts
/**
 * Fetches user data from the API.
 *
 * @param id - The user ID.
 * @param includePosts - Whether to include posts (default: false).
 */
function getUser(id: string, includePosts = false) { ... }
```

---

### 3. Async Functions
- Clarify what the promise resolves to.  

```ts
/**
 * Fetches a list of users.
 *
 * @returns A promise that resolves to an array of users.
 */
async function fetchUsers(): Promise<User[]> { ... }
```

---

### 4. Hooks Specifics
- Document the **state shape** and **returned tuple/object** clearly.  
- Mention if the hook has **side effects** (e.g., subscriptions, timers).  

---

### 5. Deprecation Notices
- Use `@deprecated` when a function/hook should not be used anymore.  

```ts
/**
 * @deprecated Use `useNewAuth` instead.
 */
export function useOldAuth() { ... }
```

---

### 6. Cross-Referencing
- Use `@see` to link related functions/hooks.  

```ts
/**
 * Logs in a user.
 *
 * @see logout
 */
function login() { ... }
```

---

### 7. Error Handling
- If a function throws, document possible errors.  

```ts
/**
 * Parses a JSON string.
 *
 * @throws Will throw an error if the string is not valid JSON.
 */
function parseJson(input: string): any { ... }
```

---

## 🔑 Key Takeaway

Every function and hook must be **self-documented** with TSDoc so that engineers
can understand and use them without digging into the implementation.  
Following these best practices ensures **clarity, maintainability, and consistency**
across the codebase.