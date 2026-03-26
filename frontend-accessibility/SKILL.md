---
name: frontend-accessibility
description: Audit and fix frontend components for WCAG 2.1 AA compliance covering semantic HTML, keyboard navigation, ARIA, color contrast, forms, and media.
---
# Skill: Frontend Accessibility (a11y)

## When to use this skill
When building or reviewing UI for accessibility compliance (WCAG 2.1 AA minimum).

## Accessibility Review Task

```xml
<task type="auto">
  <n>Accessibility review for {component/page}</n>
  <action>
    Audit against WCAG 2.1 AA standards:

    1. SEMANTIC HTML
       - Use correct elements: <button> not <div onClick>, <nav>, <main>, <article>
       - Heading hierarchy: h1 → h2 → h3 (no skipping levels)
       - Lists use <ul>/<ol>/<li>, tables use <table>/<th>/<td>
       - Forms use <label> linked to inputs (htmlFor/for attribute)
       - Links for navigation, buttons for actions

    2. KEYBOARD NAVIGATION
       - All interactive elements focusable via Tab
       - Focus order follows visual order
       - Visible focus indicator (never outline: none without replacement)
       - Escape closes modals/dropdowns
       - Enter/Space activates buttons and links
       - Arrow keys navigate within components (tabs, menus, radio groups)
       - No keyboard traps (can always Tab out)

    3. ARIA (use sparingly — semantic HTML first)
       - aria-label for icon-only buttons: <button aria-label="Close">✕</button>
       - aria-expanded for collapsible sections
       - aria-live="polite" for dynamic content updates (toasts, loaders)
       - role="alert" for error messages
       - aria-hidden="true" for decorative elements
       - Never use ARIA to fix what semantic HTML can solve

    4. COLOR & CONTRAST
       - Text contrast ratio ≥ 4.5:1 (normal text), ≥ 3:1 (large text)
       - Don't use color alone to convey information (add icons, text, patterns)
       - Check with color blindness simulator
       - Focus indicators have ≥ 3:1 contrast against background

    5. FORMS
       - Every input has a visible label (not just placeholder)
       - Error messages linked to inputs: aria-describedby
       - Required fields indicated: aria-required="true" + visual indicator
       - Error summary at top of form for screen readers
       - Autocomplete attributes for common fields (name, email, address)

    6. IMAGES & MEDIA
       - Informative images: descriptive alt text
       - Decorative images: alt="" (empty) or CSS background
       - Complex images: detailed description in surrounding text
       - Video: captions + transcript
       - Audio: transcript

    Format findings as:
    - 🔴 VIOLATION: {WCAG criterion} — {issue} — {fix}
    - 🟡 WARNING: {potential issue} — {recommendation}
    - 🟢 PASS: {what's done correctly}
  </action>
  <verify>No WCAG 2.1 AA violations, keyboard navigable, screen reader tested</verify>
  <done>Accessibility review complete, all violations addressed</done>
</task>
```

## Testing Tools

```bash
# Automated testing
npx axe-core {url}              # Automated a11y scanner
npx pa11y {url}                 # CLI accessibility tester
npx lighthouse {url} --only-categories=accessibility

# In-browser
# Chrome DevTools → Lighthouse → Accessibility
# Chrome DevTools → Elements → Accessibility panel
# axe DevTools browser extension
```

## Quick Reference

| Element | Do | Don't |
|---|---|---|
| Buttons | `<button>Click me</button>` | `<div onClick>Click me</div>` |
| Links | `<a href="/page">Go</a>` | `<span onClick>Go</span>` |
| Icons | `<button aria-label="Search"><SearchIcon /></button>` | `<button><SearchIcon /></button>` |
| Images | `<img alt="Team photo at summit">` | `<img>` or `<img alt="image">` |
| Forms | `<label for="email">Email</label><input id="email">` | `<input placeholder="Email">` |
