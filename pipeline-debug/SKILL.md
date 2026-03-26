---
name: pipeline-debug
description: Diagnose and fix CI/CD pipeline failures by systematically identifying root causes across dependency, secrets, flaky test, resource, and permissions categories.
---
# Skill: Pipeline Debugging

## When to use this skill
When a CI/CD pipeline is failing and you need to diagnose and fix it.

## Systematic Debug Process

```xml
<task type="auto">
  <n>Diagnose pipeline failure</n>
  <action>
    1. Get the full error message (not just the summary — the raw log output)
    2. Identify which step failed and what it was trying to do
    3. Check: is this a flaky test, an environment issue, or a code issue?
    4. Check: did this pipeline pass recently? What changed?
    5. Check: is this failing on all branches or just this one?
  </action>
  <verify>Root cause identified and documented</verify>
  <done>Clear diagnosis: what failed, why, and what the fix is</done>
</task>
```

## Common Failure Categories

### Dependency / Cache Issues
- Symptom: `module not found`, version conflicts, build works locally
- Fix: clear cache, pin versions, check lockfile is committed

### Secret / Environment Issues
- Symptom: auth failures, 401s, `variable not set`
- Fix: verify secret names match between pipeline YAML and secret store
- **Never log secret values** — use `echo "Secret length: ${#SECRET}"` to verify it's set

### Flaky Tests
- Symptom: fails intermittently, often on timing or external services
- Fix: add retry logic, mock external dependencies, check for race conditions
- Document flaky tests — don't just re-run until they pass

### Resource / Timeout Issues
- Symptom: OOM killed, timeout exceeded, disk full
- Fix: increase limits, add cleanup steps, optimize the slow step

### Permissions Issues
- Symptom: `permission denied`, `not authorized`, RBAC errors
- Fix: check IAM/RBAC attached to the runner service account

## Fix Template

```xml
<task type="auto">
  <n>Fix {pipeline name} - {step name} failure</n>
  <files>.github/workflows/{workflow}.yml</files>
  <action>
    Root cause: {what you found}
    Fix: {exact change to make}

    Constraints:
    - Don't change step order unless necessary
    - Keep existing cache keys — only add/modify if needed
    - Pin any new action versions explicitly
    - Test the fix logic locally before committing
  </action>
  <verify>Pipeline passes on a test branch before merging to main</verify>
  <done>Pipeline green, fix committed with explanation in commit message</done>
</task>
```
