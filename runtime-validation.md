# Runtime Validation Conventions

These rules define how runtime validation must be applied.  
They are **mandatory** for all untrusted/external data.

---

## 1. Always Validate External Data

- **Rule**: All API responses, DB results, user input, and env vars must be validated at runtime.
- **Rationale**: TypeScript only checks at compile time.

---

## 2. Use Zod (Preferred)

- **Rule**: Use Zod for all runtime validation.
- **Rule**: Prefer `.pipe()` for chaining transformations and refinements.
- **Rationale**: `.pipe()` makes schemas more composable and avoids deeply nested `.transform()` calls.

### Example: Old vs New

**Old (before `.pipe()`):**

```ts
const AgeSchema = z.string()
  .transform((val) => parseInt(val, 10))
  .refine((val) => val > 0, { message: 'Age must be positive' });
```

**New (with `.pipe()`):**

```ts
const AgeSchema = z.string()
  .pipe(z.coerce.number())
  .pipe(z.number().int().positive());
```

---

## 3. Generate Schemas from Backend

- **Rule**: Prefer generated schemas over handwritten ones.
- **Tools**:
  - Supabase → `supabase-zod-types`
  - Prisma → `zod-prisma-types`
  - OpenAPI → `zod-openapi`
  - GraphQL → `graphql-codegen` with Zod plugin

---

## 4. Validate Environment Variables

- **Rule**: Parse `import.meta.env` with Zod at startup.
- **Preferred**:

```ts
const EnvSchema = z.object({
  VITE_ENV: z.string().pipe(z.enum(['development', 'staging', 'production'])),
  VITE_SUPABASE_URL: z.string().url(),
  VITE_SUPABASE_ANON_KEY: z.string(),
  VITE_PORT: z.string().pipe(z.coerce.number().int().positive()).default('3000'),
});

export const env = EnvSchema.parse(import.meta.env);
```

---

## 5. API Contracts

- **Rule**: Define request/response schemas in `src/types/contracts/`.
- **Rule**: Validate both client and server.
- **Rule**: Use `.pipe()` for coercing query params or numeric/string conversions.

**Example:**

```ts
// Request schema with query param coercion
const GetUserRequestSchema = z.object({
  userId: z.string().uuid(),
  page: z.string().pipe(z.coerce.number().int().min(1)).default('1'),
  limit: z.string().pipe(z.coerce.number().int().max(100)).default('10'),
});

// Response schema
const GetUserResponseSchema = z.object({
  success: z.boolean(),
  profile: UserProfileSchema.optional(),
  error: z.string().optional(),
});
```

---

## 6. Error Handling

- **Rule**: Use `safeParse` for non-throwing validation.
- **Preferred**:

```ts
const result = UserSchema.safeParse(data);
if (!result.success) {
  console.error(result.error);
} else {
  const user = result.data;
}
```

---

## 7. Testing with Validation

- **Rule**: Use schemas in test factories to ensure mocks are valid.
- **Rule**: Use `.pipe()` in test schemas when coercion is needed.

**Example:**

```ts
// Schema with coercion
const TestUserSchema = z.object({
  id: z.string(),
  email: z.string().email(),
  age: z.string().pipe(z.coerce.number().int().positive()),
});

// Valid test data
const mockUser = TestUserSchema.parse({
  id: '123',
  email: 'test@example.com',
  age: '25', // coerced to number
});

// Factory function
const createMockUser = (overrides: Partial<z.infer<typeof TestUserSchema>> = {}) =>
  TestUserSchema.parse({
    id: '123',
    email: 'test@example.com',
    age: '30',
    ...overrides,
  });
```

---

# ✅ Summary

- Always validate external data  
- Use Zod for runtime validation  
- Prefer `.pipe()` for transformations/refinements (query params, env vars, test data)  
- Prefer generated schemas from backend  
- Validate env vars at startup  
- Validate API contracts on both client and server  
- Use `safeParse` for graceful error handling  
- Use schemas in tests to keep mocks valid
