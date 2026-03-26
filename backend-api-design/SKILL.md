---
name: backend-api-design
description: Design, review, or implement REST/GraphQL/gRPC APIs following consistent conventions for resource naming, error handling, auth, versioning, and performance.
---
# Skill: Backend API Design

## When to use this skill
When designing, reviewing, or implementing REST/GraphQL/gRPC APIs.

## API Design Task

```xml
<task type="auto">
  <n>Design API for {resource/feature}</n>
  <action>
    Design the API following these standards:

    1. REST CONVENTIONS
       - Use nouns for resources, verbs come from HTTP methods
       - GET /users (list), GET /users/:id (read), POST /users (create)
       - PATCH /users/:id (partial update), DELETE /users/:id (remove)
       - Nested resources for relationships: GET /users/:id/orders
       - Use plural nouns: /users not /user
       - Use kebab-case for multi-word: /order-items not /orderItems

    2. REQUEST/RESPONSE PATTERNS
       - Always return consistent envelope: { data, error, meta }
       - Pagination: cursor-based preferred, offset for simple cases
         GET /users?cursor=abc123&limit=20
         Response: { data: [...], meta: { next_cursor, has_more } }
       - Filtering: GET /users?status=active&role=admin
       - Sorting: GET /users?sort=-created_at (- prefix for desc)
       - Partial responses: GET /users?fields=id,name,email

    3. ERROR HANDLING
       - Use standard HTTP status codes correctly:
         400 Bad Request (validation), 401 Unauthorized (no auth),
         403 Forbidden (no permission), 404 Not Found, 409 Conflict,
         422 Unprocessable Entity, 429 Rate Limited, 500 Internal Error
       - Error response: { error: { code: "VALIDATION_ERROR", message: "...", details: [...] } }
       - Never expose stack traces or internal details in production

    4. AUTHENTICATION & AUTHORIZATION
       - Auth via Bearer token in Authorization header
       - Never pass tokens in query strings (they appear in logs)
       - Rate limiting with Retry-After header
       - CORS configured for allowed origins only

    5. VERSIONING
       - URL prefix: /api/v1/users (simple, explicit)
       - OR header: Accept: application/vnd.api+json;version=1

    6. VALIDATION
       - Validate ALL inputs at the boundary
       - Use schema validation (Zod, Pydantic, JSON Schema)
       - Return specific field-level errors
       - Sanitize inputs to prevent injection

    7. PERFORMANCE
       - N+1 queries: use eager loading / dataloaders
       - Pagination: never return unbounded lists
       - Caching: use ETag/If-None-Match for reads
       - Compression: gzip responses > 1KB
  </action>
  <verify>API handles happy path, error cases, auth, and pagination correctly</verify>
  <done>API designed with endpoints, request/response schemas, and error handling</done>
</task>
```

## API Review Checklist

- [ ] All endpoints use correct HTTP methods and status codes
- [ ] Input validation on every endpoint
- [ ] Auth required on protected routes
- [ ] Pagination on list endpoints (no unbounded queries)
- [ ] Consistent error format across all endpoints
- [ ] No sensitive data in URLs or logs
- [ ] Rate limiting configured
- [ ] API documented (OpenAPI/Swagger)
