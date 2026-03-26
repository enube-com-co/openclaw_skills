---
name: test-generation
description: Write unit and integration tests covering happy path, edge cases, and error cases using the project's existing test framework and style.
---
# Skill: Test Generation

## When to use this skill
When writing tests for new or existing code.

## Test Generation Task Template

```xml
<task type="auto">
  <n>Write tests for {function/module/endpoint}</n>
  <files>tests/{test_file}</files>
  <action>
    Write tests in this order:

    1. HAPPY PATH — the normal, expected use case
    2. EDGE CASES — empty, null, zero, max values, boundary conditions
    3. ERROR CASES — invalid inputs, missing required fields, auth failures
    4. INTEGRATION — if testing at the API/service level, test realistic workflows

    Rules:
    - Use the existing test framework already in the project
    - Match the existing test style/structure in the codebase
    - One clear assertion per test (or group related assertions with clear names)
    - Test names should read like documentation: "should return 404 when user not found"
    - Mock external dependencies (DB, APIs, filesystem) — don't test them here
    - Don't test private/internal functions directly — test through public API
  </action>
  <verify>All tests pass: {test command for this project}</verify>
  <done>Tests written, all passing, coverage includes happy path + key error cases</done>
</task>
```

## Test Name Patterns

```python
# Python / pytest
def test_returns_user_when_valid_id():
def test_raises_not_found_when_user_missing():
def test_raises_validation_error_when_email_invalid():
```

```go
// Go
func TestGetUser_ValidID_ReturnsUser(t *testing.T)
func TestGetUser_MissingID_ReturnsNotFound(t *testing.T)
```

```typescript
// TypeScript / Jest / Vitest
it('returns user when valid ID is provided')
it('throws NotFoundError when user does not exist')
it('throws ValidationError when email is invalid')
```

## What NOT to Test

- Implementation details (private methods, internal state)
- Third-party library behavior (trust the library has its own tests)
- Framework behavior (HTTP routing, ORM queries themselves)
- Configuration values (test that config is *used*, not what the value is)
