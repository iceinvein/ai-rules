# 🎨 Component Styling Rules (Tailwind v4)

**Stack:** React + Vite + TailwindCSS v4 + shadcn/ui  
**Goal:** Ensure consistent, maintainable, and future-proof component styling.

---

## 1. Component Source of Truth

### ✅ Always Use shadcn CLI

- All UI components must be generated via the **shadcn CLI**.
- Do **not** manually copy/paste or recreate shadcn components.
- This ensures we always use the **latest version** with correct accessibility and styling.

**Command Example:**

```bash
npx shadcn@latest add button
```

---

## 2. TailwindCSS Usage

### ✅ Utility-First Styling

- Tailwind v4 is **class-based by default** (no config file required).
- Use Tailwind utility classes for styling.
- Avoid writing custom CSS unless absolutely necessary.
- Prefer **composing utilities** over creating new classes.

**Preferred:**

```tsx
<button className="px-4 py-2 bg-primary text-primary-foreground rounded-md">
  Click Me
</button>
```

**Avoid:**

```css
/* Don't create unnecessary custom classes */
.btn {
  padding: 0.5rem 1rem;
  background-color: var(--primary);
  color: white;
  border-radius: 0.375rem;
}
```

---

## 3. Class Name Management

### ✅ Use `cn` Utility

- Always use the `cn` helper (from shadcn) to merge class names.
- Do not use string concatenation or template literals with logic.

**Preferred:**

```tsx
import { cn } from "@/lib/utils";

<Button className={cn("w-full", isActive && "bg-primary")}>
  Submit
</Button>
```

**Avoid:**

```tsx
// ❌ No template literals with logic
<Button className={`w-full ${isActive ? "bg-primary" : ""}`}>
  Submit
</Button>

// ❌ No string concatenation
<Button className={"w-full " + (isActive ? "bg-primary" : "")}>
  Submit
</Button>
```

---

## 4. Component Customization

### ✅ Extend via `className` or `variant`

- Use `className` prop to extend styles.
- Use shadcn’s `variant` system for consistent variants (e.g., `primary`, `secondary`).

**Preferred:**

```tsx
<Button variant="secondary" className="w-full">
  Submit
</Button>
```

---

## 5. Variants & Reusability

- Always prefer **variants** (via `cva` or shadcn’s built-in variant system) over duplicating classNames.  
- This keeps components consistent and avoids style drift.

**Example:**

```tsx
import { cva } from "class-variance-authority";

const badgeVariants = cva(
  "inline-flex items-center rounded-md px-2 py-1 text-xs font-medium",
  {
    variants: {
      variant: {
        default: "bg-primary text-primary-foreground",
        secondary: "bg-secondary text-secondary-foreground",
      },
    },
    defaultVariants: {
      variant: "default",
    },
  }
);

export const Badge = ({ variant, className, ...props }) => (
  <span className={cn(badgeVariants({ variant }), className)} {...props} />
);
```

---

## 6. No Inline Styles

- Never use `style={{ ... }}` for styling unless absolutely necessary (e.g., dynamic CSS variables).  
- Always prefer Tailwind utilities or variants.

---

## 7. Responsive & State Variants

- Use Tailwind’s responsive (`sm:`, `md:`, `lg:`) and state (`hover:`, `focus:`, `disabled:`) variants instead of custom CSS.

**Preferred:**

```tsx
<Button className="w-full sm:w-auto hover:bg-primary/90">
  Submit
</Button>
```

---

## 8. Accessibility First

- Always keep shadcn’s accessibility features intact (e.g., `Dialog`, `Popover`, `DropdownMenu`).  
- Don’t strip ARIA attributes or roles.  
- When extending, ensure focus states and keyboard navigation remain functional.

---

## 9. Theme Tokens Only

- Tailwind v4 uses **CSS variables for theme tokens by default**.  
- Use Tailwind theme tokens (`bg-primary`, `text-muted-foreground`) instead of raw hex values.  
- This ensures consistency and makes theme switching (light/dark) seamless.

**Avoid:**

```tsx
<div className="bg-[#1a1a1a] text-white">Bad</div>
```

**Preferred:**

```tsx
<div className="bg-background text-foreground">Good</div>
```

---

## 10. Animations

- Use Tailwind’s built-in animation utilities or shadcn’s prebuilt animations.  
- If custom animations are needed, define them in `tailwind.config.ts` (if you opt-in to config) under `extend.animation`.

---

## 11. Class Ordering

- Tailwind v4 has **built-in class sorting**.  
- `prettier-plugin-tailwindcss` is optional now, but can still be used for consistency.

---

## 12. File & Naming Conventions

- Components live in `components/ui/` (shadcn default).  
- Use **PascalCase** for component files (`Button.tsx`, `Dialog.tsx`).  
- Do not rename shadcn components — keep naming consistent with upstream.  
- Custom wrappers or app-specific components go in `components/` (outside `ui/`).  

---

## 13. Updating Components

- When shadcn releases updates, re-run the CLI to pull the latest version.  
- Do not manually patch generated components.  
- If customization is needed, wrap the component instead of editing the base.  

**Example:**

```tsx
// components/CustomButton.tsx
import { Button } from "@/components/ui/button";

export const CustomButton = (props: React.ComponentProps<typeof Button>) => (
  <Button variant="primary" className="w-full" {...props} />
);
```

---

## 14. Icons

- Use `lucide-react` (shadcn default) for icons.  
- Don’t import random SVGs inline unless necessary.  
- Wrap icons in a consistent `Icon` component if customization is needed.

---

## 15. Layout Utilities

- Use Tailwind’s flex/grid utilities for layout.  
- Avoid absolute positioning hacks unless required.  
- Tailwind v4 supports **logical properties** (`ps-`, `pe-`, `ms-`, `me-`) for RTL-friendly spacing.
