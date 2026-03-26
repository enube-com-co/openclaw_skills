---
name: frontend-component-architecture
description: Design and review frontend component hierarchies in React/Vue/Angular — component types, state placement, props design, and composition patterns.
---
# Skill: Frontend Component Architecture

## When to use this skill
When designing, building, or refactoring frontend component hierarchies in React, Vue, Angular, or similar frameworks.

## Component Design Task

```xml
<task type="auto">
  <n>Design component architecture for {feature/page}</n>
  <action>
    Follow these principles:

    1. COMPONENT HIERARCHY
       - Pages: route-level components, handle data fetching
       - Features: domain-specific logic, compose smaller components
       - UI Components: reusable, presentational, no business logic
       - Layout Components: structural (Header, Sidebar, Grid)

       Rule: data flows DOWN (props), events flow UP (callbacks/events)

    2. COMPONENT TYPES
       - Presentational: receive props, render UI, emit events
         (Button, Card, Modal, Table, FormField)
       - Container: manage state, fetch data, pass to presentational
         (UserList, OrderForm, DashboardPage)
       - Layout: structural wrappers (PageLayout, Sidebar, Grid)
       - Keep these separate — don't mix data fetching with rendering

    3. STATE MANAGEMENT
       - Local state: form inputs, toggles, UI-only state → useState/ref
       - Shared state: data needed by siblings → lift to common parent
       - Global state: auth, theme, user prefs → context/store
       - Server state: API data → use data-fetching library
         (React Query, SWR, Apollo, TanStack Query)
       - Rule: use the LEAST powerful state solution that works

    4. PROPS DESIGN
       - Keep prop interfaces small and focused
       - Use composition (children/slots) over mega-components
       - Boolean props for variants: <Button primary disabled />
       - Enum props for multiple options: <Badge variant="success" />
       - Callback props for events: <Form onSubmit={handleSubmit} />
       - Avoid prop drilling >2 levels deep → use context or composition

    5. COMPOSITION PATTERNS
       - Compound components: <Select> <Select.Option /> </Select>
       - Render props: for reusable behavior logic
       - Custom hooks: extract reusable stateful logic
       - HOCs: avoid unless wrapping third-party components

    6. FILE STRUCTURE
       ```
       src/
         components/     # shared UI components
           Button/
             Button.tsx
             Button.test.tsx
             Button.styles.ts
         features/       # domain features
           users/
             UserList.tsx
             UserForm.tsx
             useUsers.ts  # data hook
         pages/          # route-level
           UsersPage.tsx
         layouts/        # structural
           MainLayout.tsx
       ```
  </action>
  <verify>Components render correctly, props typed, no prop drilling</verify>
  <done>Component architecture designed with clear hierarchy and data flow</done>
</task>
```

## Component Review Checklist

- [ ] Single responsibility (one reason to change per component)
- [ ] Props are typed (TypeScript interfaces / PropTypes)
- [ ] No business logic in presentational components
- [ ] No inline styles for reusable components (use CSS modules/styled)
- [ ] Accessible (aria labels, keyboard navigation, semantic HTML)
- [ ] Responsive (works at mobile, tablet, desktop)
- [ ] Loading and error states handled
- [ ] Key prop on list items (not array index)
