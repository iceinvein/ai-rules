# TypeScript Conventions

These rules define how TypeScript must be used in this project.  
They are **mandatory** for all code written by humans or AI agents.

---

## 1. Type System Usage

- Prefer `type` over `interface`
- Avoid `any` (use `unknown`, `never`, `void`)
- Explicit return types for public/async functions
- Use type guards and narrowing
- Use utility types (`Partial`, `Pick`, `Omit`, etc.)

---

## 2. Typegen-First Approach (Backend-Agnostic)

- Always use generated types (Supabase, Prisma, Drizzle, OpenAPI, GraphQL)
- Centralize generated types in `src/types/generated/`
- Use barrel exports for clean imports
- Compose request/response contracts from generated types

---

## 3. Error Handling Types

- Use `unknown` in `catch`
- Normalize errors with a shared `AppError` type

---

## 4. React Component Conventions

- Explicit prop types
- Avoid `React.FC`
- Explicitly type `children`
- Use default values in destructuring

---

## 5. Environment Variables

- Strongly type `import.meta.env`
- Use unions for environment modes

---

## 6. Compiler & Linting

- Enable strict mode in `tsconfig.json`
- Use ESLint + Prettier with `@typescript-eslint`

---

## 7. Advanced Patterns

- Discriminated unions for state machines
- Exhaustive checks with `never`
- Branded types for domain-specific primitives

---
✅ **Summary**:  
Typegen-first, no `any`, explicit return types, strict compiler settings, barrel exports, typed contracts, and advanced patterns for maintainability.
