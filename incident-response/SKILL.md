---
name: incident-response
description: Guide production incident response through four phases: triage blast radius, apply mitigation, communicate status, and run post-mortem.
---
# Skill: Incident Response

## When to use this skill
When responding to a production incident, alert, or service degradation.

## Phase 1: Triage (first 5 minutes)

```xml
<task type="auto">
  <n>Assess blast radius</n>
  <action>
    1. What service(s) are affected?
    2. What percentage of traffic / users are impacted?
    3. What is the current error rate vs baseline?
    4. Is this getting worse, stable, or improving?
    5. When did it start? Correlate with recent deploys/changes.
  </action>
  <verify>Blast radius documented in STATE.md</verify>
  <done>Clear picture of scope: which services, how many users, direction of trend</done>
</task>
```

## Phase 2: Mitigation

```xml
<task type="auto">
  <n>Stabilize the system</n>
  <action>
    Options in order of preference (least risky first):
    1. Feature flag off → disable the affected feature
    2. Traffic shift → route away from unhealthy instances
    3. Rollback → revert to last known good deploy
    4. Scale up → add capacity if load-related
    5. Manual fix → only if above options aren't available

    Document chosen action in STATE.md before applying.
  </action>
  <verify>Error rate returning to baseline, no new alerts firing</verify>
  <done>System stable. Mitigation applied and documented.</done>
</task>
```

## Phase 3: Communication

Draft a status update using this format:
```
[TIME] - [SEVERITY] - [SERVICE NAME]
Status: Investigating / Identified / Mitigating / Resolved
Impact: [Who is affected and how]
Current action: [What we're doing right now]
Next update: [When]
```

## Phase 4: Post-Incident (after resolution)

Create a post-mortem document covering:

1. Timeline (what happened, when)
2. Root cause
3. Contributing factors
4. What went well
5. What went wrong
6. Action items (with owners and due dates)
