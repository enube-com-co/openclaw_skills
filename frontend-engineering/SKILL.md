---
name: frontend-engineering
description: Design and review frontend component architecture, state management, performance, and accessibility for React/Vue/Angular applications.
---
# Skill: Frontend Engineering

## When to use this skill
When designing components, choosing state solutions, optimizing performance, or auditing accessibility in frontend applications.

---

## Component Architecture

```xml
<task type="auto">
  <n>Design component architecture for {feature/page}</n>
  <action>
    1. HIERARCHY
       - Pages: route-level, handle data fetching
       - Features: domain-specific logic, compose smaller components
       - UI Components: reusable, presentational, no business logic
       - Layout: structural (Header, Sidebar, Grid)
       Rule: data flows DOWN (props), events flow UP (callbacks)

    2. COMPONENT TYPES
       - Presentational: receive props, render UI, emit events
       - Container: manage state, fetch data, pass to presentational
       - Keep them separate — no data fetching in presentational components

    3. PROPS DESIGN
       - Keep interfaces small and focused
       - Use composition (children/slots) over mega-components
       - Avoid prop drilling > 2 levels → use context or composition
       - Callback props for events: <Form onSubmit={handleSubmit} />

    4. FILE STRUCTURE
       src/
         components/     # shared UI (Button/, Modal/)
         features/       # domain logic (users/, orders/)
         pages/          # route-level
         layouts/        # structural wrappers
  </action>
  <verify>Components render correctly, props typed, no prop drilling</verify>
  <done>Component hierarchy designed with clear data flow</done>
</task>
```

### Component Review Checklist
- [ ] Single responsibility per component
- [ ] Props are typed (TypeScript interfaces)
- [ ] No business logic in presentational components
- [ ] Loading and error states handled
- [ ] key prop on list items (not array index)

---

## State Management

**Decision Tree:**
```
UI-only state (toggle, hover, form input)?
  YES → useState / ref

Server/API data (users, orders)?
  YES → React Query / SWR / TanStack Query

Shared between siblings?
  Few levels → lift to common parent
  Many levels → Context or global store

Truly global (auth, theme, locale)?
  Simple → Context
  Complex → Zustand / Redux Toolkit
```

### Patterns
```typescript
// Server state — React Query
const { data, isLoading, error } = useQuery({
  queryKey: ['users', filters],
  queryFn: () => fetchUsers(filters),
  staleTime: 5 * 60 * 1000,
});

// Optimistic update
const mutation = useMutation({
  mutationFn: updateUser,
  onMutate: async (newUser) => {
    await queryClient.cancelQueries(['users']);
    const previous = queryClient.getQueryData(['users']);
    queryClient.setQueryData(['users'], (old) => /* optimistic */);
    return { previous };
  },
  onError: (err, vars, ctx) => queryClient.setQueryData(['users'], ctx.previous),
  onSettled: () => queryClient.invalidateQueries(['users']),
});

// Global state — Zustand
const useStore = create((set) => ({
  theme: 'light',
  toggleTheme: () => set((s) => ({ theme: s.theme === 'light' ? 'dark' : 'light' })),
}));
```

### State Anti-Patterns
- Storing server data in global state instead of a query library
- Prop drilling > 3 levels
- Putting everything in global state "just in case"
- Fetching in useEffect without cleanup (use a query library)
- Duplicating state — derived data should be computed, not stored

---

## Performance Optimization

```xml
<task type="auto">
  <n>Audit frontend performance for {page/app}</n>
  <action>
    1. BUNDLE SIZE
       - Analyze: npx vite-bundle-visualizer
       - Code-split routes: lazy(() => import('./pages/Dashboard'))
       - Replace moment.js → date-fns; lodash → lodash-es
       - Dynamic import for heavy features (charts, editors, maps)
       - Target: initial JS < 200KB gzipped

    2. RENDERING
       - React: useMemo/useCallback/React.memo for expensive components
       - Virtualize lists > 100 items: react-window or @tanstack/virtual
       - Debounce search, resize, scroll handlers

    3. LOADING
       - Critical CSS inlined in <head>
       - Fonts: font-display: swap, preload critical fonts
       - Images: lazy loading, srcset, WebP/AVIF
       - <link rel="preconnect"> for external API domains

    4. CORE WEB VITALS targets
       - LCP < 2.5s, INP < 200ms, CLS < 0.1
       - Measure with Lighthouse, web-vitals library
  </action>
  <verify>Lighthouse score improved, bundle reduced, Core Web Vitals pass</verify>
  <done>Performance audit with prioritized fixes</done>
</task>
```

---

## Accessibility (WCAG 2.1 AA)

```xml
<task type="auto">
  <n>Accessibility review for {component/page}</n>
  <action>
    1. SEMANTIC HTML (always first)
       - <button> not <div onClick>, <nav>, <main>, <article>
       - Heading hierarchy: h1 → h2 → h3 (no skipping)
       - <label> linked to every input (htmlFor)
       - Links for navigation, buttons for actions

    2. KEYBOARD NAVIGATION
       - All interactive elements focusable via Tab
       - Visible focus indicator (never outline: none without replacement)
       - Escape closes modals/dropdowns
       - No keyboard traps

    3. ARIA (use sparingly — semantic HTML first)
       - aria-label for icon-only buttons
       - aria-expanded for collapsibles
       - aria-live="polite" for dynamic updates (toasts, loading)
       - role="alert" for error messages
       - aria-hidden="true" for decorative elements

    4. COLOR & CONTRAST
       - Text ≥ 4.5:1 contrast (normal), ≥ 3:1 (large text)
       - Never use color alone to convey meaning
       - Focus indicators ≥ 3:1 contrast

    5. FORMS
       - Every input has a visible label (not just placeholder)
       - Error messages linked via aria-describedby
       - Required fields: aria-required="true" + visual indicator

    Format findings:
    - 🔴 VIOLATION: {WCAG criterion} — {issue} — {fix}
    - 🟡 WARNING: {potential issue} — {recommendation}
    - 🟢 PASS: {what's done correctly}
  </action>
  <verify>No WCAG 2.1 AA violations, keyboard navigable</verify>
  <done>Accessibility review complete, violations addressed</done>
</task>
```

### Quick Reference

| Element | Do | Don't |
|---|---|---|
| Buttons | `<button>Click</button>` | `<div onClick>Click</div>` |
| Icons | `<button aria-label="Search"><Icon /></button>` | `<button><Icon /></button>` |
| Images | `<img alt="Team photo">` | `<img>` or `<img alt="image">` |
| Forms | `<label for="email">Email</label><input id="email">` | `<input placeholder="Email">` |
