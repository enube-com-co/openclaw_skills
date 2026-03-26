---
name: code-review
description: Review PRs, diffs, or code for security, correctness, performance, and maintainability issues using a prioritized findings format.
---
# Skill: Code Review

## When to use this skill
When reviewing a PR, diff, or piece of code for quality, security, and correctness.

## Review Task Template

```xml
<task type="auto">
  <n>Review {PR/file/feature name}</n>
  <action>
    Review in this order — stop and flag blockers immediately:

    1. SECURITY (blockers)
       - Injection vulnerabilities (SQL, command, LDAP)
       - Auth/authz bypass possibilities
       - Secrets hardcoded or logged
       - Insecure deserialization
       - Missing input validation on user-controlled data

    2. CORRECTNESS (blockers)
       - Logic errors that produce wrong results
       - Race conditions (shared mutable state, no locking)
       - Resource leaks (unclosed connections, file handles)
       - Error cases not handled or swallowed silently
       - Off-by-one errors, boundary conditions

    3. PERFORMANCE (warnings)
       - N+1 database queries
       - Missing database indexes for new query patterns
       - Unbounded loops or recursion
       - Unnecessary serialization/deserialization in hot paths

    4. MAINTAINABILITY (suggestions)
       - Functions doing too many things
       - Missing or misleading comments on complex logic
       - Magic numbers/strings without constants
       - Test coverage for new code paths

    Format findings as:
    - 🔴 BLOCKER: {issue} — {why it matters} — {suggested fix}
    - 🟡 WARNING: {issue} — {why it matters} — {suggested fix}
    - 🟢 SUGGESTION: {issue} — {optional improvement}
  </action>
  <verify>All blockers explicitly addressed before approval</verify>
  <done>Review complete with categorized findings. LGTM only if zero blockers.</done>
</task>
```

## PR Approval Criteria

✅ **Approve if:**
- Zero blockers
- Tests cover the new/changed behavior
- No secrets or sensitive data
- Follows existing code style

❌ **Request changes if:**
- Any security blocker
- Any correctness blocker
- No tests for non-trivial logic
- Hardcoded values that should be config

💬 **Comment but approve if:**
- Performance suggestions that aren't urgent
- Style improvements that are preferences
- Refactoring opportunities to file as follow-up
