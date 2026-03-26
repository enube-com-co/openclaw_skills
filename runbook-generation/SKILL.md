---
name: runbook-generation
description: Create operational runbooks for alerts and services with structured triage steps, mitigation options, recovery verification, and escalation procedures.
---
# Skill: Runbook Generation

## When to use this skill
When creating or updating operational runbooks for alerts, services, or procedures.

## Runbook Template

```markdown
# Runbook: {Alert or Procedure Name}

## Overview
One paragraph: what this runbook covers and when it's used.

## Trigger
- Alert name: {exact alert name in monitoring system}
- Threshold: {what value triggers this}
- Severity: P1 / P2 / P3

## Impact
- **Users affected:** {% of users or specific user segments}
- **Services affected:** {list services}
- **Business impact:** {revenue, SLO, customer-facing degradation}

## Prerequisites
- Access to: {list systems needed}
- On-call rotation: {link to schedule}

## Triage Steps
1. {First check — most likely cause}
   ```bash
   {exact command to run}
   ```
   Expected output: {what normal looks like}

2. {Second check}
   ...

## Mitigation Steps
### Option A: {Most common fix}
```bash
{exact commands}
```

### Option B: {Fallback}
...

## Recovery Verification
- [ ] Error rate back to baseline ({target}%)
- [ ] Latency p99 back to baseline ({target}ms)
- [ ] {Service}-specific health check passing
- [ ] Alert resolved in monitoring system

## Escalation
If not resolved in {X} minutes:
1. Page: {team/person}
2. Channel: {Slack channel}
3. Bridge: {incident bridge link}

## Post-Incident
- File post-mortem if P1/P2: {link to template}
- Update this runbook if steps were wrong/missing

## Revision History
| Date | Author | Change |
|---|---|---|
| {date} | {author} | Initial version |
```

## Runbook Creation Task

```xml
<task type="auto">
  <n>Create runbook for {alert name}</n>
  <files>runbooks/{service}-{alert-slug}.md</files>
  <action>
    Use the runbook template above.
    Fill in all sections. Get exact alert names from monitoring config.
    Include real commands tested against the actual environment.
    Cross-reference with existing runbooks for consistency.
  </action>
  <verify>runbook file exists, all template sections filled (no placeholder text remaining)</verify>
  <done>Runbook ready for team review at runbooks/{filename}.md</done>
</task>
```
