---
name: git-documentation
description: Write clear pull request descriptions and generate user-facing changelogs from git history following Keep a Changelog conventions.
---
# Skill: Git Documentation

## When to use this skill
When writing pull request descriptions or generating changelogs from git commit history.

---

## PR Description

```xml
<task type="auto">
  <n>Write PR description for {branch/feature name}</n>
  <action>
    Analyze the diff and write using this template:

    ## What
    One sentence. What does this PR do?

    ## Why
    Why is this needed? Link to ticket/issue if applicable.

    ## How
    Key implementation decisions. What approach was taken and why?
    What alternatives were considered?

    ## Testing
    How was this tested? What scenarios are covered?
    Any cases intentionally NOT covered (and why)?

    ## Risks
    What could go wrong? Rollback plan?
    (Skip if genuinely low risk)

    ## Screenshots (if UI changes)
    Before / After

    ## Checklist
    - [ ] Tests added/updated
    - [ ] No secrets or sensitive data in code or logs
    - [ ] Docs updated (if public API changed)
    - [ ] Backwards compatible (or migration plan documented)

    Rules:
    - Be specific: "fixed null pointer in UserService when profile is incomplete"
      not "fixed bug"
    - Reviewers shouldn't need to read the code to understand the change
    - If it needs paragraphs of explanation, the PR might be too big
  </action>
  <verify>Description covers what/why/how, checklist complete</verify>
  <done>PR description ready to paste into GitHub/GitLab</done>
</task>
```

---

## Changelog Generation

```xml
<task type="auto">
  <n>Generate changelog for {version/date range}</n>
  <action>
    1. Get commit history:
       git log {from_tag}..HEAD --oneline --no-merges

    2. Categorize each commit:
       - 🚀 Features (feat: commits)
       - 🐛 Bug Fixes (fix: commits)
       - ⚡ Performance (perf: commits)
       - 🔒 Security (security-related changes)
       - 🔧 Maintenance (chore:, refactor:, ci: — skip if no user impact)

    3. Rewrite for non-technical audience:
       BAD: "fix(auth): handle null pointer in JWT validation"
       GOOD: "Fixed an issue where some users couldn't log in"

    4. Format:
       ## {version} — {date}
       ### 🚀 New Features
       - {user-friendly description}
       ### 🐛 Bug Fixes
       - {description}
       ### ⚡ Improvements
       - {description}

    Rules:
    - Skip internal refactoring unless it affects UX
    - Group related commits into single entries
    - Link to PRs/issues where available
    - One sentence per entry
  </action>
  <verify>Covers all user-facing changes, no technical jargon</verify>
  <done>CHANGELOG.md updated with new version entry</done>
</task>
```

### Keep a Changelog Labels
- `Added` — new features
- `Changed` — changes to existing functionality
- `Deprecated` — soon-to-be removed
- `Removed` — removed features
- `Fixed` — bug fixes
- `Security` — vulnerability fixes
