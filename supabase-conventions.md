# 🗄️ Supabase Rules & Conventions

**Stack:** Supabase (Postgres + Edge Functions) + React (Vite)  
**Goal:** Ensure type safety, consistency, and maintainability when working with Supabase.

---

## 1. Database Schema & Migrations

### ✅ Use SQL Migrations

- All schema changes must be done via **SQL migration files** (`supabase/migrations/`).
- Never edit the database directly in the dashboard for production projects.
- Each migration must be **idempotent** and **descriptive**.

**Preferred:**

```sql
-- 20230815_add_profiles_table.sql
create table if not exists profiles (
  id uuid primary key references auth.users(id) on delete cascade,
  full_name text,
  avatar_url text,
  created_at timestamp with time zone default now()
);
```

---

### ✅ Naming Conventions

- **Tables:** `snake_case` (e.g., `user_profiles`)  
- **Columns:** `snake_case` (e.g., `created_at`)  
- **Enums:** `snake_case` (e.g., `user_role`)  
- **Constraints/Indexes:** descriptive names (`profiles_user_id_fkey`)  

---

### ✅ Use Enums for Fixed Values

- Use Postgres enums for fixed sets of values instead of free-text columns.

```sql
create type user_role as enum ('admin', 'editor', 'viewer');
```

---

## 2. Type Safety

### ✅ Always Use Generated Types

- Use Supabase’s generated `Database` types for queries.
- Never manually redefine schema types.

```typescript
import type { Database } from "@/types/database";

type Profile = Database["public"]["Tables"]["profiles"]["Row"];
type ProfileInsert = Database["public"]["Tables"]["profiles"]["Insert"];
type ProfileUpdate = Database["public"]["Tables"]["profiles"]["Update"];
```

---

### ✅ Explicit Return Types

- Always type async functions that interact with Supabase.

```typescript
const getProfile = async (id: string): Promise<Profile | null> => {
  const { data, error } = await supabase
    .from("profiles")
    .select("*")
    .eq("id", id)
    .single();

  if (error) throw error;
  return data;
};
```

---

## 3. Auth Rules

### ✅ Use Supabase Auth Helpers

- Use `@supabase/auth-helpers-react` for session management in React.
- Never manually store tokens in `localStorage`.

```typescript
import { useUser } from "@supabase/auth-helpers-react";

const Profile = () => {
  const user = useUser();
  return <div>{user?.email}</div>;
};
```

---

### ✅ RLS (Row-Level Security)

- Always enable **RLS** on tables.
- Define **policies** for read/write access.
- Never rely on client-side checks for security.

**Example:**

```sql
-- Allow users to read their own profile
create policy "Users can view their own profile"
on profiles for select
using (auth.uid() = id);

-- Allow users to update their own profile
create policy "Users can update their own profile"
on profiles for update
using (auth.uid() = id);
```

---

## 4. Edge Functions

### ✅ Typed Input/Output

- Define request/response types in `shared/types.ts`.
- Validate input with `zod` or similar.

```typescript
export type UpdateProfileRequest = {
  userId: string;
  updates: ProfileUpdate;
};

export type UpdateProfileResponse = {
  success: boolean;
  profile?: Profile;
  error?: string;
};
```

---

### ✅ No Business Logic in Client

- Keep business logic in **Edge Functions**.
- Client should only call functions, not implement rules.

---

### ✅ Secure Edge Functions

- Always validate `req.headers.get("Authorization")`.
- Use `supabaseClient.auth.getUser()` inside functions to enforce auth.

---

## 5. Query Rules

### ✅ Use React Query for Data Fetching

- Wrap Supabase queries in React Query hooks.
- Never use `useEffect` + `useState` for server data.

```typescript
const useProfile = (id: string) =>
  useQuery({
    queryKey: ["profile", id],
    queryFn: () => getProfile(id),
  });
```

---

### ✅ Avoid `select("*")`

- Always select only the fields you need.
- Improves performance and reduces over-fetching.

```typescript
const { data } = await supabase
  .from("profiles")
  .select("id, full_name")
  .eq("id", userId)
  .single();
```

---

### ✅ Handle Errors Explicitly

- Always check for `error` in Supabase responses.
- Normalize errors before handling.

```typescript
const { data, error } = await supabase.from("profiles").select("*");
if (error) throw normalizeSupabaseError(error);
```

---

## 6. Storage Rules

### ✅ Use Buckets with RLS

- Always enable RLS on storage buckets.
- Use signed URLs for private files.

```typescript
const { data } = await supabase.storage
  .from("avatars")
  .createSignedUrl("user123.png", 60);
```

---

### ✅ File Naming

- Use `userId/filename` convention for uploads.
- Prevent collisions and ensure traceability.

---

## 7. Performance & Indexing

### ✅ Add Indexes for Query Patterns

- Always add indexes for frequently queried columns.

```sql
create index on profiles (email);
```

---

### ✅ Use `limit` and Pagination

- Never fetch unbounded rows.
- Use `.range()` or `.limit()` for pagination.

```typescript
const { data } = await supabase
  .from("posts")
  .select("*")
  .range(0, 9); // first 10 posts
```

---

## 8. Project Structure

### ✅ Recommended Layout

    supabase/
      migrations/        # SQL migration files
      functions/         # Edge functions
        hello-world/
          index.ts
          types.ts       # Function-specific types
      types/
        database.ts      # Supabase generated types
        index.ts         # Barrel export for shared types
    src/
      lib/
        supabase.ts      # Supabase client initialization
      hooks/
        useProfile.ts    # React Query hooks wrapping Supabase queries
      services/
        profile.ts       # Business logic calling Supabase/Edge functions

---

### ✅ Shared Types

- Keep shared request/response types in `supabase/functions/<fn>/types.ts` or `supabase/types/`.
- Import them in both client and server for end-to-end type safety.

---

### ✅ Supabase Client

- Initialize Supabase client in `src/lib/supabase.ts`.
- Never re-initialize in multiple files.

```typescript
import { createClient } from "@supabase/supabase-js";

export const supabase = createClient(
  import.meta.env.VITE_SUPABASE_URL,
  import.meta.env.VITE_SUPABASE_ANON_KEY
);
```

---

## 9. Do’s and Don’ts

**Do:**

- Use SQL migrations for schema changes.  
- Use Supabase generated types.  
- Enable RLS and write policies for every table.  
- Use React Query for data fetching.  
- Validate input in Edge Functions.  
- Organize code with a clear folder structure.  

**Don’t:**

- Don’t edit schema directly in dashboard (except for prototyping).  
- Don’t use `any` for Supabase responses.  
- Don’t disable RLS in production.  
- Don’t fetch `*` unless debugging.  
- Don’t put business logic in the client.  
- Don’t duplicate Supabase client initialization.  

---

# ✅ Summary

- **Schema:** SQL migrations, snake_case, enums for fixed values.  
- **Types:** Always use Supabase generated types.  
- **Auth:** Use helpers, enforce RLS, never trust client-only checks.  
- **Edge Functions:** Typed input/output, validate with `zod`, enforce auth.  
- **Queries:** Use React Query, avoid `*`, handle errors explicitly.  
- **Storage:** Use RLS, signed URLs, and `userId/filename` convention.  
- **Performance:** Add indexes, use pagination.  
- **Structure:** Clear folder layout for migrations, functions, types, and client.
