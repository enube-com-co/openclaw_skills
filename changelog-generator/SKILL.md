---
name: changelog-generator
description: Generate user-facing changelogs from git commit history, transforming technical commits into customer-friendly release notes following Keep a Changelog format.
---
# Skill: Changelog Generator

## When to use this skill
When generating user-facing changelogs from git commit history. Transforms technical commits into customer-friendly release notes.

## Changelog Generation Task

```xml
<task type="auto">
  <n>Generate changelog for {version/date range}</n>
  <action>
    1. Read git log for the specified range:
       git log {from_tag}..HEAD --oneline --no-merges

    2. Categorize each commit:
       - 🚀 Features (feat: commits)
       - 🐛 Bug Fixes (fix: commits)
       - ⚡ Performance (perf: commits)
       - 🔒 Security (security-related changes)
       - 📝 Documentation (docs: commits)
       - 🔧 Maintenance (chore:, refactor:, ci: commits)

    3. Rewrite each entry for a non-technical audience:
       - BAD: "fix(auth): handle null pointer in JWT validation"
       - GOOD: "Fixed an issue where some users couldn't log in"

    4. Format as:
       ## {version} — {date}
       ### 🚀 New Features
       - {user-friendly description}
       ### 🐛 Bug Fixes
       - {user-friendly description}
       ### ⚡ Improvements
       - {user-friendly description}

    Rules:
    - Skip internal refactoring unless it affects user experience
    - Group related commits into single entries
    - Link to PRs/issues where available
    - Keep descriptions under 1 sentence each
  </action>
  <verify>Changelog covers all user-facing changes, no technical jargon</verify>
  <done>CHANGELOG.md updated with new version entry</done>
</task>
```

## Keep a Changelog Format

Follow [keepachangelog.com](https://keepachangelog.com) conventions:
- `Added` for new features
- `Changed` for changes in existing functionality
- `Deprecated` for soon-to-be removed features
- `Removed` for now removed features
- `Fixed` for any bug fixes
- `Security` for vulnerability fixes
