---
name: frontend-state-management
description: Choose and implement the right state management solution — local state, server state, shared state, or global store — with anti-pattern detection.
---
# Skill: Frontend State Management

## When to use this skill
When choosing, implementing, or refactoring state management in a frontend application.

## State Management Decision Tree

```
Is this UI-only state (toggle, form input, hover)?
  YES → Local component state (useState, ref)
  NO ↓

Is it server/API data (users, orders, content)?
  YES → Server state library (React Query, SWR, Apollo, TanStack Query)
  NO ↓

Is it shared between sibling components?
  YES, few levels → Lift state to common parent
  YES, many levels → Context or global store
  NO ↓

Is it truly global (auth, theme, locale, feature flags)?
  YES → Context (simple) or global store (complex)
```

## Patterns by Category

### Local State
```typescript
// Simple values
const [isOpen, setIsOpen] = useState(false);

// Complex objects — use reducer
const [state, dispatch] = useReducer(formReducer, initialState);

// Rule: if you have >3 related useState calls, switch to useReducer
```

### Server State
```typescript
// React Query / TanStack Query
const { data, isLoading, error } = useQuery({
  queryKey: ['users', filters],
  queryFn: () => fetchUsers(filters),
  staleTime: 5 * 60 * 1000, // 5 min cache
});

// Mutations with optimistic updates
const mutation = useMutation({
  mutationFn: updateUser,
  onMutate: async (newUser) => {
    await queryClient.cancelQueries(['users']);
    const previous = queryClient.getQueryData(['users']);
    queryClient.setQueryData(['users'], (old) => /* optimistic update */);
    return { previous };
  },
  onError: (err, vars, context) => {
    queryClient.setQueryData(['users'], context.previous);
  },
  onSettled: () => queryClient.invalidateQueries(['users']),
});
```

### Global State
```typescript
// React Context — for simple global values
const AuthContext = createContext<AuthState>(null);

// Zustand — for more complex global state (simpler than Redux)
const useStore = create((set) => ({
  theme: 'light',
  toggleTheme: () => set((s) => ({ theme: s.theme === 'light' ? 'dark' : 'light' })),
}));

// Redux Toolkit — for large apps with complex state logic
// Only if you need: time-travel debugging, complex middlewares, large teams
```

## Anti-Patterns to Flag

- Storing server data in global state instead of using a query library
- Prop drilling >3 levels deep (use context or composition)
- Putting everything in global state ("just in case")
- Using Redux for simple apps that only need Context
- Fetching data in useEffect without cleanup/caching (use a query library)
- Mutating state directly (always create new references)
- Duplicating state (derived data should be computed, not stored)

## Review Checklist

- [ ] Each piece of state lives at the correct level (local/shared/global/server)
- [ ] Server data uses a data-fetching library with caching
- [ ] No prop drilling beyond 2-3 levels
- [ ] Loading and error states handled for all async data
- [ ] Optimistic updates for user-facing mutations
- [ ] No stale closures in event handlers/effects
