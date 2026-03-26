---
name: root-cause-tracing
description: Trace errors back to their root cause by walking the call chain, identifying the earliest divergence point, verifying the cause, and documenting the findings.
---
# Skill: Root Cause Tracing

## When to use this skill
When errors occur deep in execution and you need to trace back to find the original trigger.

## Root Cause Analysis Task

```xml
<task type="auto">
  <n>Trace root cause of {error/symptom}</n>
  <action>
    Systematic trace-back analysis:

    1. IDENTIFY THE SYMPTOM
       - What is the exact error message?
       - Where in the stack trace does it occur?
       - When did it start? (correlate with deploys, config changes)

    2. TRACE THE CALL CHAIN
       - Start at the error location
       - Walk backwards through the call stack
       - At each level, ask: "Is this where the bug is, or is it propagating from deeper?"
       - Document the chain: A calls B calls C → error in C, but root cause in A

    3. IDENTIFY THE ROOT CAUSE
       - The root cause is the EARLIEST point where behavior diverges from expected
       - Common root causes:
         * Bad input data (upstream service sending malformed data)
         * State corruption (race condition, missing initialization)
         * Configuration error (wrong env var, stale config)
         * Resource exhaustion (memory, connections, file handles)
         * Dependency failure (external service down, version mismatch)

    4. VERIFY THE ROOT CAUSE
       - Can you reproduce the error by manipulating the suspected root cause?
       - Does fixing the root cause eliminate the symptom?
       - Are there other code paths that could trigger the same symptom from a different root cause?

    5. DOCUMENT
       Write to STATE.md:
       - Symptom: {what was observed}
       - Root cause: {what actually went wrong}
       - Evidence: {how you confirmed it}
       - Fix: {what needs to change}
       - Prevention: {how to prevent recurrence}
  </action>
  <verify>Fix applied, symptom no longer reproducible, no regressions</verify>
  <done>Root cause identified, fixed, and documented in STATE.md</done>
</task>
```

## Tips
- For complex traces, consider escalating to deeper reasoning or specialized models.
- For large log analysis, ensure the model's context window can accommodate the data volume.
- Don't fix symptoms — always trace to the root cause first.
