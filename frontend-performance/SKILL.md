---
name: frontend-performance
description: Audit and optimize frontend applications for bundle size, rendering performance, loading strategy, caching, and Core Web Vitals (LCP, INP, CLS).
---
# Skill: Frontend Performance Optimization

## When to use this skill
When optimizing page load times, runtime performance, or bundle size in frontend applications.

## Performance Audit Task

```xml
<task type="auto">
  <n>Audit frontend performance for {page/app}</n>
  <action>
    Check each category in order of impact:

    1. BUNDLE SIZE
       - Analyze bundle: npx vite-bundle-visualizer / npx webpack-bundle-analyzer
       - Code split routes: lazy(() => import('./pages/Dashboard'))
       - Tree-shake unused exports (check for barrel file bloat)
       - Replace heavy libraries with lighter alternatives:
         moment.js → date-fns or dayjs
         lodash (full) → lodash-es (tree-shakeable) or native methods
       - Dynamic import for heavy features (charts, editors, maps)
       - Target: initial JS bundle < 200KB gzipped

    2. RENDERING PERFORMANCE
       - Avoid unnecessary re-renders:
         React: useMemo, useCallback, React.memo for expensive components
         Vue: computed properties, v-once for static content
       - Virtualize long lists (>100 items): react-window, @tanstack/virtual
       - Debounce expensive handlers (search, resize, scroll)
       - Avoid layout thrashing (batch DOM reads/writes)
       - Use CSS containment for complex isolated components

    3. LOADING STRATEGY
       - Critical CSS inlined in <head>
       - Fonts: font-display: swap, preload critical fonts
       - Images: lazy loading, srcset for responsive, WebP/AVIF format
       - Prefetch next-likely routes on hover/idle
       - Use <link rel="preconnect"> for external API domains

    4. CACHING
       - Static assets: immutable hashed filenames, Cache-Control: max-age=31536000
       - API responses: stale-while-revalidate pattern (React Query/SWR)
       - Service worker for offline-capable resources
       - LocalStorage/IndexedDB for persistent client-side data

    5. CORE WEB VITALS
       - LCP (Largest Contentful Paint) < 2.5s
       - FID/INP (Interaction to Next Paint) < 200ms
       - CLS (Cumulative Layout Shift) < 0.1
       - Measure with: Lighthouse, web-vitals library, CrUX

    Format findings as:
    - 🔴 CRITICAL: {issue} — {impact on metrics} — {fix}
    - 🟡 IMPORTANT: {issue} — {estimated improvement} — {fix}
    - 🟢 NICE-TO-HAVE: {issue} — {fix}
  </action>
  <verify>Lighthouse score improved, bundle size reduced, Core Web Vitals pass</verify>
  <done>Performance audit complete with prioritized optimizations</done>
</task>
```

## Quick Wins Checklist

- [ ] Images optimized (WebP, srcset, lazy loading)
- [ ] Route-based code splitting implemented
- [ ] No unused dependencies in bundle
- [ ] Fonts preloaded with font-display: swap
- [ ] Static assets have cache-busting hashes
- [ ] Lists with >50 items are virtualized
- [ ] No synchronous blocking scripts in <head>
