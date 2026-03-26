---
name: webapp-testing
description: Write Playwright end-to-end tests for web applications covering page load, user interactions, form validation, responsive behavior, and error states.
---
# Skill: Web Application Testing (Playwright)

## When to use this skill
When testing local web applications using Playwright for verifying frontend functionality, debugging UI behavior, and capturing screenshots.

## Setup

Ensure Playwright is installed in the project:
```bash
npm install -D @playwright/test
npx playwright install
```

## Test Task Template

```xml
<task type="auto">
  <n>Write Playwright tests for {feature/page}</n>
  <files>tests/e2e/{test_file}.spec.ts</files>
  <action>
    Write end-to-end tests covering:

    1. PAGE LOAD
       - Verify the page loads without errors
       - Check critical elements are visible
       - Verify no console errors

    2. USER INTERACTIONS
       - Click buttons, fill forms, navigate links
       - Test keyboard navigation and accessibility
       - Verify loading states and transitions

    3. FORM VALIDATION
       - Submit with valid data → success
       - Submit with invalid data → error messages shown
       - Test required fields, format validation, edge cases

    4. RESPONSIVE BEHAVIOR
       - Test at mobile (375px), tablet (768px), desktop (1280px)
       - Verify layout adapts correctly

    5. ERROR STATES
       - Network failures (mock with route.abort())
       - API errors (mock with route.fulfill({ status: 500 }))
       - Empty states (no data)

    Rules:
    - Use page.locator() with accessible selectors (role, label, text)
    - Avoid fragile selectors (.class-name, nth-child)
    - Use expect(locator).toBeVisible() over waitForSelector
    - Add screenshots on failure: page.screenshot({ path: 'failure.png' })
  </action>
  <verify>npx playwright test {test_file} --reporter=list</verify>
  <done>E2E tests passing, covers happy path + error states</done>
</task>
```

## Screenshot Capture for Debugging

```typescript
// Capture current state
await page.screenshot({ path: 'debug-screenshot.png', fullPage: true });

// Capture specific element
await page.locator('#my-component').screenshot({ path: 'component.png' });
```

## Common Patterns

```typescript
// Wait for network idle (page fully loaded)
await page.goto('http://localhost:3000', { waitUntil: 'networkidle' });

// Mock API response
await page.route('**/api/users', route =>
  route.fulfill({ status: 200, body: JSON.stringify([{ id: 1, name: 'Test' }]) })
);

// Test form submission
await page.getByLabel('Email').fill('test@example.com');
await page.getByRole('button', { name: 'Submit' }).click();
await expect(page.getByText('Success')).toBeVisible();
```
