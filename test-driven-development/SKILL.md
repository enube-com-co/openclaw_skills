---
name: test-driven-development
description: Apply the TDD red-green-refactor cycle — write failing tests first, implement minimal code to pass, then refactor while keeping tests green.
---
# Skill: Test-Driven Development

## When to use this skill
When implementing any feature or bugfix. Write tests BEFORE writing implementation code.

## TDD Workflow

### Step 1: Red — Write a failing test
```xml
<task type="auto">
  <n>Write failing test for {feature/fix}</n>
  <files>tests/{test_file}</files>
  <action>
    Write a test that:
    1. Describes the expected behavior in the test name
    2. Sets up the minimal required state
    3. Calls the function/endpoint that doesn't exist yet
    4. Asserts the expected outcome

    The test MUST fail. If it passes, the test is wrong or the feature already exists.
    Run the test to confirm it fails with a meaningful error.
  </action>
  <verify>Test fails with expected error (not a syntax error)</verify>
  <done>Failing test written and confirmed red</done>
</task>
```

### Step 2: Green — Write minimal code to pass
```xml
<task type="auto">
  <n>Implement minimal code to pass test</n>
  <files>{implementation_file}</files>
  <action>
    Write the MINIMUM code needed to make the test pass.
    - Don't optimize yet
    - Don't handle edge cases yet (unless the test covers them)
    - Don't refactor yet
    - Just make it work
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
    Now improve the code:
    - Extract functions if logic is complex
    - Rename for clarity
    - Remove duplication
    - Improve performance if obvious

    Run tests after EVERY change. If any test fails, undo the last change.
  </action>
  <verify>All tests still pass after refactoring</verify>
  <done>Code clean, tests green, ready for next cycle</done>
</task>
```

## Rules
- Never write implementation code without a failing test first
- Each TDD cycle should take 5-15 minutes
- If a cycle takes longer than 15 minutes, the task is too big — break it down
- Commit after each green step
