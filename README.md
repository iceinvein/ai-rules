# 🤖 AI Development Rules & Conventions

A comprehensive collection of rules, conventions, and best practices for AI agents developing modern React applications with TypeScript, Supabase, and related technologies.

## 📋 Overview

This repository contains standardized guidelines that ensure consistent, maintainable, and high-quality code generation across AI-powered development workflows. These rules are designed to be used by AI agents (like Claude, GPT-4, etc.) when generating code for React applications.

## 🎯 Purpose

- **Consistency**: Ensure all AI-generated code follows the same patterns and conventions
- **Quality**: Maintain high standards for type safety, accessibility, and performance
- **Maintainability**: Create code that's easy to understand, modify, and extend
- **Best Practices**: Enforce modern development practices and architectural patterns

## 📁 Rule Categories

### Core Development
- **[TypeScript Conventions](typescript-conventions.md)** - Type safety, utility types, and strict mode rules
- **[React Rules](react-rules.md)** - Component structure, hooks, and performance guidelines
- **[State Management](state-management.md)** - Server state (React Query) vs client state (Nanostores)
- **[Runtime Validation](runtime-validation.md)** - Zod schemas and data validation patterns

### Styling & UI
- **[Component Styling](component-styling.md)** - TailwindCSS v4 and shadcn/ui guidelines
- **[Markup Rules](markup-rules.md)** - Semantic HTML, accessibility, and SEO best practices

### Backend Integration
- **[Supabase Conventions](supabase-conventions.md)** - Database schema, migrations, and type safety
- **[Supabase + TanStack](supabase-tanstack.md)** - Data fetching patterns and real-time updates
- **[React + Supabase Flow](react-supabase-flow.md)** - Architecture diagram and data flow

### Testing & Documentation
- **[E2E Playwright](e2e-playwright.md)** - End-to-end testing with Page Object Models
- **[TSDoc Documentation](ts-docs.md)** - Function and hook documentation standards

## 🛠️ Technology Stack

These rules are optimized for the following stack:

- **Frontend**: React 18+ with Vite
- **Language**: TypeScript (strict mode)
- **Styling**: TailwindCSS v4 + shadcn/ui
- **State Management**: 
  - Server State: TanStack Query (React Query)
  - Client State: Nanostores
- **Backend**: Supabase (PostgreSQL + Edge Functions)
- **Validation**: Zod for runtime validation
- **Testing**: Vitest (unit) + Playwright (E2E)
- **Package Manager**: pnpm (preferred)

## 🚀 Usage

### For AI Agents

1. **Load these rules** into your context when working on React projects
2. **Reference specific rule files** based on the task at hand
3. **Follow the conventions** outlined in each document
4. **Prioritize type safety** and accessibility in all generated code

### For Developers

1. **Review the rules** before starting a new project
2. **Use as a checklist** during code reviews
3. **Customize rules** to fit your specific project needs
4. **Share with your team** to ensure consistency

## 📖 Key Principles

### Type Safety First
- Always use TypeScript strict mode
- Prefer `type` over `interface`
- Validate all external data with Zod
- Generate types from backend schemas

### Component Architecture
- Small, focused, reusable components
- Proper separation of concerns
- Server state vs client state distinction
- Accessibility-first markup

### Performance & UX
- Efficient data fetching with React Query
- Proper loading and error states
- Optimistic updates where appropriate
- Real-time updates via query invalidation

### Developer Experience
- Comprehensive TSDoc documentation
- Clear naming conventions
- Consistent file structure
- Automated testing strategies

## 🔄 Data Flow Architecture

The rules enforce a specific data flow pattern:

```
Supabase DB → Edge Functions → Services → React Query Hooks → Components
                                      ↘ Nanostores (client state)
```

## 📝 Contributing

To add or modify rules:

1. Follow the existing document structure
2. Include practical examples (✅ Do / ❌ Don't)
3. Explain the rationale behind each rule
4. Update this README if adding new categories

## 📄 License

These rules are provided as-is for educational and development purposes. Adapt them to fit your project's specific needs.

---

**Note**: These rules are designed to work with AI coding assistants and can be customized based on your project requirements and team preferences.