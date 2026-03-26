---
name: github-automation
description: Create and manage GitHub Actions workflows, PR automation, branch protection, and release pipelines following security and caching best practices.
---
# Skill: GitHub Automation

## When to use this skill
When automating GitHub workflows: creating/managing issues, PRs, releases, Actions workflows, and repository configuration.

## GitHub Actions Workflow Template

```xml
<task type="auto">
  <n>Create GitHub Actions workflow for {purpose}</n>
  <files>.github/workflows/{workflow-name}.yml</files>
  <action>
    Write a GitHub Actions workflow following these standards:

    1. TRIGGER CONFIGURATION
       - Use specific triggers (push to main, PR to main, tag push)
       - Avoid trigger on all branches unless necessary
       - Use path filters to skip irrelevant changes

    2. JOB STRUCTURE
       - Fail fast: lint → unit tests → build → integration tests → deploy
       - Use matrix strategy for multi-version/multi-platform testing
       - Set explicit timeouts on all jobs

    3. SECURITY
       - Pin action versions to full SHA: uses: actions/checkout@{sha}
       - Use GITHUB_TOKEN with minimum required permissions
       - Never echo secrets — use environment variables
       - Set permissions block at workflow or job level

    4. CACHING
       - Cache dependencies (node_modules, pip cache, Go modules)
       - Use content-based cache keys (hash of lockfile)
       - Include restore-keys for partial cache hits

    5. ARTIFACTS
       - Upload build artifacts for downstream jobs
       - Set retention-days to avoid storage costs

    Example:
    ```yaml
    name: CI
    on:
      push:
        branches: [main]
      pull_request:
        branches: [main]

    permissions:
      contents: read

    jobs:
      test:
        runs-on: ubuntu-latest
        timeout-minutes: 15
        steps:
          - uses: actions/checkout@v4
          - uses: actions/setup-node@v4
            with:
              node-version: 20
              cache: npm
          - run: npm ci
          - run: npm test
    ```
  </action>
  <verify>Workflow syntax valid: actionlint .github/workflows/{file}</verify>
  <done>GitHub Actions workflow created and validated</done>
</task>
```

## PR Automation Task

```xml
<task type="auto">
  <n>Automate PR workflow for {repository}</n>
  <action>
    Set up PR automation:
    1. Auto-assign reviewers based on CODEOWNERS
    2. Required status checks before merge
    3. Auto-label based on changed files
    4. PR template with checklist
    5. Branch protection rules
  </action>
  <verify>PR created with all automations active</verify>
  <done>PR workflow automated with labels, reviews, and checks</done>
</task>
```

## Useful gh CLI Commands

```bash
# Create issue
gh issue create --title "Bug: ..." --body "..." --label bug

# Create PR
gh pr create --title "feat: ..." --body "..." --base main

# List failing checks
gh pr checks

# Create release
gh release create v1.0.0 --generate-notes

# View workflow runs
gh run list --workflow=ci.yml --limit=5
```
