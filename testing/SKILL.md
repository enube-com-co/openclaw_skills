---
name: testing
description: Write unit/integration tests, apply TDD red-green-refactor, and create Playwright E2E tests covering happy path, edge cases, and error states.
---
# Skill: Testing

## When to use this skill
When writing tests, practicing TDD, or creating end-to-end tests for web applications.

---

## Unit & Integration Tests

```xml
<task type="auto">
  <n>Write tests for {function/module/endpoint}</n>
  <files>tests/{test_file}</files>
  <action>
    Write tests in this order:
    1. HAPPY PATH — normal, expected use case
    2. EDGE CASES — empty, null, zero, max, boundary conditions
    3. ERROR CASES — invalid inputs, missing fields, auth failures
    4. INTEGRATION — realistic workflows at API/service level

    Rules:
    - Use the existing test framework in the project
    - Match existing test style/structure
    - One clear assertion per test (or clearly grouped)
    - Test names read like documentation: "should return 404 when user not found"
    - Mock external dependencies (DB, APIs) — don't test them here
    - Test through public API, not private internals
  </action>
  <verify>All tests pass: {test command}</verify>
  <done>Tests written, all passing, covering happy path + key error cases</done>
</task>
```

### Test Name Patterns
```python
# Python / pytest
def test_returns_user_when_valid_id(): ...
def test_raises_not_found_when_user_missing(): ...
```
```typescript
// TypeScript / Jest / Vitest
it('returns user when valid ID is provided')
it('throws NotFoundError when user does not exist')
```
```go
// Go
func TestGetUser_ValidID_ReturnsUser(t *testing.T)
func TestGetUser_MissingID_ReturnsNotFound(t *testing.T)
```

### What NOT to Test
- Private methods or internal state
- Third-party library behavior
- Framework behavior (HTTP routing, ORM queries themselves)
- Configuration values (test that config is *used*, not what the value is)

---

## Test-Driven Development (TDD)

### Step 1: Red — Write a failing test
```xml
<task type="auto">
  <n>Write failing test for {feature/fix}</n>
  <files>tests/{test_file}</files>
  <action>
    Write a test that:
    1. Describes expected behavior in the name
    2. Sets up minimal required state
    3. Calls the function/endpoint that doesn't exist yet
    4. Asserts the expected outcome

    The test MUST fail. If it passes, the test or feature already exists.
    Run it to confirm: a meaningful error, not a syntax error.
  </action>
  <verify>Test fails with expected error (not syntax error)</verify>
  <done>Failing test written and confirmed red</done>
</task>
```

### Step 2: Green — Minimal implementation
```xml
<task type="auto">
  <n>Implement minimal code to pass test</n>
  <files>{implementation_file}</files>
  <action>
    Write the MINIMUM code to make the test pass.
    Don't optimize, handle edge cases, or refactor yet — just make it work.
  </action>
  <verify>Test passes</verify>
  <done>Test green with minimal implementation</done>
</task>
```

### Step 3: Refactor — Clean up while green
```xml
<task type="auto">
  <n>Refactor {feature} keeping tests green</n>
  <files>{implementation_file}, tests/{test_file}</files>
  <action>
    Improve the code: extract functions, rename for clarity, remove duplication.
    Run tests after EVERY change. If any test fails, undo the last change.
  </action>
  <verify>All tests still pass after refactoring</verify>
  <done>Code clean, tests green</done>
</task>
```

### TDD Rules
- Never write implementation code without a failing test first
- Each cycle: 5–15 minutes. Longer = task too big, break it down
- Commit after each green step

---

## End-to-End Testing (Playwright)

```xml
<task type="auto">
  <n>Write Playwright tests for {feature/page}</n>
  <files>tests/e2e/{test_file}.spec.ts</files>
  <action>
    Cover in order:
    1. PAGE LOAD — critical elements visible, no console errors
    2. USER INTERACTIONS — clicks, forms, navigation, loading states
    3. FORM VALIDATION — valid data → success; invalid → error messages shown
    4. RESPONSIVE — test at 375px, 768px, 1280px
    5. ERROR STATES — network failures (route.abort()), API errors (route.fulfill({ status: 500 }))

    Rules:
    - Use page.locator() with accessible selectors (role, label, text)
    - Avoid fragile selectors (.class-name, nth-child)
    - Use expect(locator).toBeVisible() over waitForSelector
  </action>
  <verify>npx playwright test {test_file} --reporter=list</verify>
  <done>E2E tests passing, covers happy path + error states</done>
</task>
```

### Common Playwright Patterns
```typescript
// Wait for full page load
await page.goto('http://localhost:3000', { waitUntil: 'networkidle' });

// Mock API response
await page.route('**/api/users', route =>
  route.fulfill({ status: 200, body: JSON.stringify([{ id: 1, name: 'Test' }]) })
);

// Form submission
await page.getByLabel('Email').fill('test@example.com');
await page.getByRole('button', { name: 'Submit' }).click();
await expect(page.getByText('Success')).toBeVisible();

// Screenshot on failure
await page.screenshot({ path: 'failure.png', fullPage: true });
```
