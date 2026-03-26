---
name: backend-error-handling
description: Implement error handling patterns, structured logging, health checks, retry logic, and observability in backend services.
---
# Skill: Error Handling & Observability

## When to use this skill
When implementing error handling patterns, structured logging, or observability in backend services.

## Error Handling Patterns

```xml
<task type="auto">
  <n>Implement error handling for {service/module}</n>
  <action>
    Apply these patterns:

    1. ERROR HIERARCHY
       - Define domain-specific error types (not just generic Error)
       - Base: AppError { code, message, statusCode, context }
       - Subtypes: ValidationError, NotFoundError, AuthError, ConflictError
       - Never throw raw strings or generic errors

    2. ERROR BOUNDARIES
       - Catch at service boundaries (controllers, handlers, middleware)
       - Let errors propagate naturally through business logic
       - Never swallow errors silently (no empty catch blocks)
       - Log errors with context at the catch point

    3. ERROR RESPONSE FORMAT
       ```json
       {
         "error": {
           "code": "VALIDATION_ERROR",
           "message": "Human-readable description",
           "details": [
             { "field": "email", "message": "Invalid format" }
           ],
           "requestId": "req_abc123"
         }
       }
       ```
       - Never expose stack traces in production
       - Include requestId for tracing

    4. STRUCTURED LOGGING
       ```
       logger.error("Failed to process order", {
         orderId: order.id,
         userId: user.id,
         error: err.message,
         stack: err.stack,  // only in non-production or internal logs
         duration_ms: elapsed
       })
       ```
       - Log levels: ERROR (action needed), WARN (unusual but handled),
         INFO (business events), DEBUG (development only)
       - Always include: timestamp, requestId, userId, action
       - Never log: passwords, tokens, PII, full request bodies

    5. RETRY PATTERNS
       - Idempotency keys for retryable operations
       - Exponential backoff with jitter for external calls
       - Circuit breaker for failing dependencies
       - Dead letter queue for failed async operations

    6. HEALTH CHECKS
       - /health — basic liveness (app is running)
       - /ready — readiness (dependencies connected)
       - Include dependency status: db, cache, external APIs
  </action>
  <verify>All error paths tested, structured logs verified, health checks respond</verify>
  <done>Error handling consistent across service, logging structured, health checks active</done>
</task>
```

## Observability Checklist

- [ ] Structured JSON logs (not plain text)
- [ ] Request IDs propagated through the stack
- [ ] Error rates tracked as metrics
- [ ] Latency percentiles (p50, p95, p99) measured
- [ ] Health check endpoints implemented
- [ ] Dependency health monitored (db, cache, APIs)
- [ ] Alerts configured for error rate spikes
