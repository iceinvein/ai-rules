# Supabase + TanStack Query Standard

## Purpose

This document defines the **rules and best practices** for using Supabase in
React applications. The goal is to ensure **consistency, maintainability, and
real-time correctness** by enforcing a single data-fetching pattern.

---

## 🔹 Rules

### 1. No Direct Fetching in Components

- ❌ Do **not** use `useEffect + useState + supabase.from(...)` inside
  components.
- ✅ Always use **React Query hooks** (`useQuery`, `useMutation`) to fetch or
  mutate data.

---

### 2. Centralize Fetching Logic

- All Supabase queries must be wrapped in **custom hooks**.
- Hooks can be:
  - **Generic**: `useSupabaseQuery`, `useSupabaseMutation`
  - **Table-specific**: `useTodos`, `useUsers`, etc.
- Components should **only consume hooks**, never call Supabase directly.

---

### 3. Real-time Updates = Query Invalidation

- ❌ Do not manually patch local state with Supabase events.
- ✅ Use Supabase real-time channels to **invalidate queries** via
  `queryClient.invalidateQueries([tableName])`.

---

### 4. Mutations Must Invalidate Queries

- After `insert`, `update`, or `delete`, always invalidate the related query.
- This ensures UI stays in sync without manual state updates.

---

### 5. Error & Loading States

- Errors should be thrown inside hooks so React Query handles them.
- Components should only handle `isLoading`, `isError`, and `data`.

---

## 🔹 Example: Generic Hooks

```javascript
// src/hooks/useSupabaseQuery.js
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";
import { supabase } from "../lib/supabaseClient";

export function useSupabaseQuery({ key, table, select = "*", filters = {} }) {
  return useQuery({
    queryKey: [key],
    queryFn: async () => {
      let query = supabase.from(table).select(select);
      for (const [col, val] of Object.entries(filters)) {
        query = query.eq(col, val);
      }
      const { data, error } = await query;
      if (error) throw error;
      return data;
    },
  });
}

export function useSupabaseMutation({ table, type = "insert" }) {
  const queryClient = useQueryClient();

  return useMutation({
    mutationFn: async (payload) => {
      let query = supabase.from(table);
      if (type === "insert") query = query.insert(payload).select();
      if (type === "update")
        query = query.update(payload.values).eq("id", payload.id).select();
      if (type === "delete") query = query.delete().eq("id", payload.id);

      const { data, error } = await query;
      if (error) throw error;
      return data;
    },
    onSuccess: () => {
      queryClient.invalidateQueries([table]);
    },
  });
}
```

---

## 🔹 Example: Real-time Subscription

```javascript
// src/hooks/useRealtimeTodos.js
import { useEffect } from "react";
import { supabase } from "../lib/supabaseClient";
import { useQueryClient } from "@tanstack/react-query";

export function useRealtimeTodos() {
  const queryClient = useQueryClient();

  useEffect(() => {
    const channel = supabase
      .channel("todos-changes")
      .on(
        "postgres_changes",
        { event: "*", schema: "public", table: "todos" },
        () => {
          queryClient.invalidateQueries(["todos"]);
        }
      )
      .subscribe();

    return () => {
      supabase.removeChannel(channel);
    };
  }, [queryClient]);
}
```

---

## 🔹 Example: Component Usage

```javascript
import {
  useSupabaseQuery,
  useSupabaseMutation,
} from "../hooks/useSupabaseQuery";
import { useRealtimeTodos } from "../hooks/useRealtimeTodos";

export default function Todos() {
  const { data: todos, isLoading } = useSupabaseQuery({
    key: "todos",
    table: "todos",
  });

  const addTodo = useSupabaseMutation({ table: "todos", type: "insert" });
  useRealtimeTodos();

  if (isLoading) return <p>Loading...</p>;

  return (
    <>
      <ul>
        {todos?.map((t) => (
          <li key={t.id}>{t.task}</li>
        ))}
      </ul>
      <button onClick={() => addTodo.mutate([{ task: "New Task" }])}>
        Add Todo
      </button>
    </>
  );
}
```

---

## ✅ Summary

- **Always use React Query hooks** for Supabase data access.  
- **Never fetch directly in components.**  
- **Use real-time events only to invalidate queries.**  
- **Mutations must invalidate queries.**  

This ensures a **consistent, scalable, and bug-resistant** data layer across the
entire app.
