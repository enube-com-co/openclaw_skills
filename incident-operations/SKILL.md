---
name: incident-operations
description: Respond to production incidents (triage, mitigate, communicate, post-mortem), trace root causes, and generate operational runbooks.
---
# Skill: Incident Operations

## When to use this skill
When responding to a production incident, tracing errors to their root cause, or creating runbooks for alerts and services.

---

## Incident Response

### Phase 1: Triage (first 5 minutes)
```xml
<task type="auto">
  <n>Assess blast radius</n>
  <action>
    1. Which service(s) are affected?
    2. What % of traffic/users are impacted?
    3. Current error rate vs baseline?
    4. Getting worse, stable, or improving?
    5. When did it start? Correlate with recent deploys/config changes.
  </action>
  <verify>Blast radius documented in STATE.md</verify>
  <done>Scope clear: which services, how many users, trend direction</done>
</task>
```

### Phase 2: Mitigation
```xml
<task type="auto">
  <n>Stabilize the system</n>
  <action>
    Options in order of preference (least risky first):
    1. Feature flag off → disable the affected feature
    2. Traffic shift → route away from unhealthy instances
    3. Rollback → revert to last known good deploy
    4. Scale up → add capacity if load-related
    5. Manual fix → only if none of the above apply

    Document chosen action in STATE.md before applying.
  </action>
  <verify>Error rate returning to baseline, no new alerts firing</verify>
  <done>System stable. Mitigation applied and documented.</done>
</task>
```

### Phase 3: Communication Template
```
[TIME] - [SEVERITY] - [SERVICE NAME]
Status: Investigating / Identified / Mitigating / Resolved
Impact: [Who is affected and how]
Current action: [What we're doing right now]
Next update: [When]
```

### Phase 4: Post-Mortem
1. Timeline (what happened, when)
2. Root cause
3. Contributing factors
4. What went well / what went wrong
5. Action items (with owners and due dates)

---

## Root Cause Tracing

```xml
<task type="auto">
  <n>Trace root cause of {error/symptom}</n>
  <action>
    1. IDENTIFY THE SYMPTOM
       - Exact error message?
       - Where in the stack trace?
       - When did it start? (correlate with deploys, config changes)

    2. TRACE THE CALL CHAIN
       - Start at the error location
       - Walk backwards: "Is this where the bug is, or propagating from deeper?"
       - Document: A calls B calls C → error in C, root cause in A

    3. IDENTIFY ROOT CAUSE — the EARLIEST point where behavior diverges
       Common causes:
       - Bad input data (upstream service sending malformed data)
       - State corruption (race condition, missing initialization)
       - Configuration error (wrong env var, stale config)
       - Resource exhaustion (memory, connections, file handles)
       - Dependency failure (external service down, version mismatch)

    4. VERIFY
       - Can you reproduce by manipulating the suspected root cause?
       - Does fixing it eliminate the symptom?

    5. DOCUMENT in STATE.md:
       - Symptom, Root cause, Evidence, Fix, Prevention
  </action>
  <verify>Fix applied, symptom no longer reproducible, no regressions</verify>
  <done>Root cause identified, fixed, documented in STATE.md</done>
</task>
```

**Tips:** Don't fix symptoms — trace to root cause first. For large log analysis, ensure context window can accommodate the data.

---

## Runbook Generation

```xml
<task type="auto">
  <n>Create runbook for {alert name}</n>
  <files>runbooks/{service}-{alert-slug}.md</files>
  <action>
    Write a runbook using this template:

    # Runbook: {Alert or Procedure Name}

    ## Overview
    What this runbook covers and when it's used.

    ## Trigger
    - Alert name: {exact name in monitoring}
    - Threshold: {what triggers it}
    - Severity: P1 / P2 / P3

    ## Impact
    - Users affected: {%}
    - Services affected: {list}
    - Business impact: {revenue, SLO, customer-facing degradation}

    ## Triage Steps
    1. {First check — most likely cause}
       ```bash
       {exact command}
       ```
       Expected output: {what normal looks like}

    ## Mitigation Steps
    ### Option A: {Most common fix}
    ### Option B: {Fallback}

    ## Recovery Verification
    - [ ] Error rate back to baseline
    - [ ] Latency p99 back to baseline
    - [ ] Alert resolved in monitoring

    ## Escalation
    If not resolved in {X} minutes: page {team}, channel {slack}, bridge {link}

    ## Post-Incident
    - File post-mortem if P1/P2
    - Update runbook if steps were wrong/missing

    Rules: Use real commands tested against the actual environment. No placeholder text.
  </action>
  <verify>All template sections complete, no placeholder text remaining</verify>
  <done>Runbook ready for team review</done>
</task>
```
