---
name: pr-description
description: Write clear, complete pull request descriptions covering what, why, how, testing, risks, and a merge checklist.
---
# Skill: PR Description Writing

## When to use this skill
When writing or improving pull request descriptions.

## PR Description Task

```xml
<task type="auto">
  <n>Write PR description for {branch/feature name}</n>
  <action>
    Analyze the diff and write a PR description using this template:

    ## What
    One sentence. What does this PR do?

    ## Why
    Why is this change needed? Link to ticket/issue if applicable.

    ## How
    Key implementation decisions. What approach was taken and why?
    What alternatives were considered?

    ## Testing
    How was this tested? What scenarios were covered?
    Are there any cases intentionally NOT covered (and why)?

    ## Risks
    What could go wrong? Any rollback plan?
    (Skip section if genuinely low risk)

    ## Screenshots (if UI changes)
    Before / After

    ## Checklist
    - [ ] Tests added/updated
    - [ ] No secrets or sensitive data in code or logs
    - [ ] Docs updated (if public API changed)
    - [ ] Backwards compatible (or migration plan documented)

    Rules:
    - Be specific — not "fixed bug" but "fixed null pointer in UserService when profile is incomplete"
    - Reviewers shouldn't need to read the code to understand the change
    - Link related PRs, issues, or design docs
    - Keep it short — if it needs paragraphs of explanation, the PR might be too big
  </action>
  <verify>Description covers what/why/how, checklist complete</verify>
  <done>PR description ready to paste into GitHub/GitLab</done>
</task>
```
