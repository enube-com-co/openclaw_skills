---
name: backend-engineering
description: Design and implement REST APIs, error handling, structured logging, health checks, and database schemas following production-grade standards.
---
# Skill: Backend Engineering

## When to use this skill
When designing or implementing APIs, error handling, observability, or database schemas/queries in backend services.

---

## API Design

```xml
<task type="auto">
  <n>Design API for {resource/feature}</n>
  <action>
    1. REST CONVENTIONS
       - Nouns for resources, HTTP methods as verbs
       - GET /users, POST /users, PATCH /users/:id, DELETE /users/:id
       - Nested: GET /users/:id/orders
       - Plural, kebab-case: /order-items

    2. REQUEST/RESPONSE
       - Consistent envelope: { data, error, meta }
       - Pagination: cursor-based preferred
         GET /users?cursor=abc&limit=20 → { data, meta: { next_cursor, has_more } }
       - Filtering: GET /users?status=active&sort=-created_at
       - Field selection: GET /users?fields=id,name

    3. ERROR HANDLING
       - 400 validation, 401 unauthenticated, 403 forbidden, 404 not found
       - 409 conflict, 422 unprocessable, 429 rate limited, 500 internal
       - Error body: { error: { code, message, details, requestId } }
       - Never expose stack traces in production

    4. AUTH & SECURITY
       - Bearer token in Authorization header (never in query params)
       - Validate ALL inputs at the boundary (Zod, Pydantic, JSON Schema)
       - Rate limiting with Retry-After header
       - CORS for allowed origins only

    5. VERSIONING
       - URL prefix: /api/v1/users

    6. PERFORMANCE
       - Eager loading to avoid N+1
       - Pagination on all list endpoints
       - ETag/If-None-Match for caching
       - Gzip responses > 1KB
  </action>
  <verify>Handles happy path, errors, auth, and pagination correctly</verify>
  <done>Endpoints defined with request/response schemas and error handling</done>
</task>
```

### API Review Checklist
- [ ] Correct HTTP methods and status codes
- [ ] Input validation on every endpoint
- [ ] Auth on protected routes
- [ ] Pagination on all list endpoints
- [ ] Consistent error format
- [ ] No sensitive data in URLs or logs
- [ ] Rate limiting configured

---

## Error Handling & Observability

```xml
<task type="auto">
  <n>Implement error handling for {service/module}</n>
  <action>
    1. ERROR HIERARCHY
       - Define typed errors: AppError { code, message, statusCode, context }
       - Subtypes: ValidationError, NotFoundError, AuthError, ConflictError
       - Never throw raw strings

    2. BOUNDARIES
       - Catch at service boundaries (controllers, middleware)
       - Let errors propagate through business logic naturally
       - Never swallow errors silently

    3. STRUCTURED LOGGING
       - Always include: timestamp, requestId, userId, action, duration_ms
       - Never log: passwords, tokens, PII, full request bodies
       - Log levels: ERROR (action needed), WARN (handled), INFO (business events), DEBUG (dev only)

    4. RETRY PATTERNS
       - Idempotency keys for retryable operations
       - Exponential backoff with jitter for external calls
       - Circuit breaker for failing dependencies
       - Dead letter queue for failed async ops

    5. HEALTH CHECKS
       - GET /health — liveness (app running)
       - GET /ready — readiness (dependencies connected)
       - Include: db, cache, external API status
  </action>
  <verify>Error paths tested, structured logs verified, health checks respond</verify>
  <done>Error handling consistent, logging structured, health checks active</done>
</task>
```

### Observability Checklist
- [ ] Structured JSON logs
- [ ] Request IDs propagated through the stack
- [ ] Error rates tracked as metrics
- [ ] Latency p50/p95/p99 measured
- [ ] Health check endpoints implemented
- [ ] Alerts configured for error rate spikes

---

## Database Design & Query Optimization

```xml
<task type="auto">
  <n>Design database schema for {feature/domain}</n>
  <action>
    1. SCHEMA
       - 3NF: no repeating groups, no partial/transitive dependencies
       - UUIDs for public-facing IDs, auto-increment for internal PKs
       - TIMESTAMPTZ for all timestamps
       - ENUM/CHECK for known value sets
       - JSON/JSONB only for truly dynamic data

    2. INDEXES
       - Index columns used in WHERE, JOIN, ORDER BY
       - Composite indexes: most selective column first
       - Partial indexes for filtered queries: WHERE status = 'active'
       - Foreign keys on all relationships with ON DELETE behavior

    3. MIGRATIONS
       - One migration per logical change, always reversible
       - Add columns nullable first → backfill → add NOT NULL
       - Never rename columns in production (add new, migrate, drop old)
       - No destructive changes without backup confirmation

    4. QUERY OPTIMIZATION
       - EXPLAIN ANALYZE before and after
       - Sequential scans on large tables → add index
       - N+1 queries → rewrite as JOINs
       - Always add LIMIT
       - Use EXISTS over IN for subqueries
  </action>
  <verify>Schema supports required queries, migrations are reversible</verify>
  <done>Schema with indexes, constraints, and migration plan</done>
</task>
```

### Database Anti-Patterns
- `SELECT *` in production queries
- Missing indexes on foreign keys
- N+1 queries in ORM loops
- Unbounded queries without LIMIT
- String concatenation in SQL (injection risk)
- Storing money as FLOAT (use DECIMAL)
- Missing created_at/updated_at
