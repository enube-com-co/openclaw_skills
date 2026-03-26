---
name: dockerfile-optimize
description: Optimize Dockerfiles using multi-stage builds, proper layer ordering, slim base images, non-root users, .dockerignore, and health checks.
---
# Skill: Dockerfile Optimization

## When to use this skill
When reviewing, creating, or optimizing Dockerfiles.

## Optimization Checklist

```xml
<task type="auto">
  <n>Optimize Dockerfile for {service}</n>
  <files>Dockerfile, .dockerignore</files>
  <action>
    Apply in this order:

    1. MULTI-STAGE BUILD
       - Stage 1 (builder): install all deps, compile, build artifacts
       - Stage 2 (runtime): copy only the built artifacts, no build tools

    2. BASE IMAGE
       - Use specific tags: `python:3.12.3-slim` not `python:latest`
       - Prefer slim/alpine variants for runtime stage
       - Match the version your app was developed against

    3. LAYER ORDERING (for cache efficiency)
       - COPY dependency files first (requirements.txt, package.json, go.mod)
       - RUN install dependencies
       - COPY source code last (changes most often)

    4. NON-ROOT USER
       - Add user: `RUN addgroup -S app && adduser -S app -G app`
       - Switch before CMD: `USER app`

    5. .dockerignore
       - Exclude: .git, node_modules, .env, tests, docs, *.md
       - Include only what's needed to build

    6. HEALTHCHECK
       - Add HEALTHCHECK for any long-running service

    7. LABELS
       - Add: maintainer, version, build date
  </action>
  <verify>
    docker build -t test-image .
    docker run --rm test-image id  (should not be root)
    docker image inspect test-image (check size vs before)
  </verify>
  <done>Image builds, runs as non-root, size reduced, layers ordered for cache efficiency</done>
</task>
```

## Before / After Example

**Before (common anti-patterns):**
```dockerfile
FROM node:latest
COPY . .
RUN npm install
RUN npm run build
CMD ["node", "server.js"]
```

**After (optimized):**
```dockerfile
# Build stage
FROM node:20.11.0-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY src/ ./src/
RUN npm run build

# Runtime stage
FROM node:20.11.0-slim AS runtime
WORKDIR /app
RUN addgroup -S app && adduser -S app -G app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER app
HEALTHCHECK --interval=30s --timeout=3s CMD wget -q -O- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```
