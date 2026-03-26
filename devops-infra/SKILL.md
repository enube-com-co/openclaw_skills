---
name: devops-infra
description: Optimize Dockerfiles, review Terraform plans, debug CI/CD pipelines, and create GitHub Actions workflows following security and reliability best practices.
---
# Skill: DevOps & Infrastructure

## When to use this skill
When working with Docker, Terraform/IaC, CI/CD pipelines, or GitHub Actions workflows.

---

## Dockerfile Optimization

```xml
<task type="auto">
  <n>Optimize Dockerfile for {service}</n>
  <files>Dockerfile, .dockerignore</files>
  <action>
    1. MULTI-STAGE BUILD
       - Stage 1 (builder): install deps, compile, build artifacts
       - Stage 2 (runtime): copy only built artifacts — no build tools

    2. BASE IMAGE
       - Specific tags: python:3.12.3-slim not python:latest
       - Prefer slim/alpine for runtime stage

    3. LAYER ORDER (cache efficiency)
       - COPY dependency files first (requirements.txt, package.json)
       - RUN install
       - COPY source last (changes most often)

    4. SECURITY
       - Non-root user: RUN addgroup -S app && adduser -S app -G app; USER app
       - HEALTHCHECK for long-running services
       - .dockerignore: exclude .git, node_modules, .env, tests, docs

    5. VERIFY
       docker build -t test-image .
       docker run --rm test-image id   # should not be root
       docker image inspect test-image # check size vs before
  </action>
  <done>Image builds, runs as non-root, layers ordered for cache efficiency</done>
</task>
```

**Before → After example:**
```dockerfile
# Before (anti-pattern)
FROM node:latest
COPY . .
RUN npm install && npm run build
CMD ["node", "server.js"]

# After (optimized)
FROM node:20.11.0-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY src/ ./src/
RUN npm run build

FROM node:20.11.0-slim AS runtime
WORKDIR /app
RUN addgroup -S app && adduser -S app -G app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER app
HEALTHCHECK --interval=30s --timeout=3s CMD wget -q -O- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```

---

## Terraform Review

```xml
<task type="auto">
  <n>Review Terraform plan for {environment/change}</n>
  <action>
    Review in this order:

    1. DESTRUCTIONS (🔴 always flag)
       - Any resource destroyed or replaced
       - Confirm intentional, rollback plan exists

    2. SECURITY (🔴 blockers)
       - Security groups: 0.0.0.0/0 on ports 22, 3306, 5432, 6379
       - IAM: * actions or * resources
       - S3 buckets with public access
       - Encryption disabled on RDS, EBS, S3
       - Secrets as plain text (use SSM / Secrets Manager refs)

    3. COST (🟡 warnings)
       - Oversized instance types
       - Unnecessary NAT Gateways
       - Multi-AZ where not required

    4. RELIABILITY (🟡 warnings)
       - Single-AZ for production services
       - Missing backup retention on RDS
       - No deletion protection on production databases

    5. TAGGING (🟢 suggestions)
       - Required tags: Environment, Team, CostCenter, ManagedBy
  </action>
  <verify>All blockers resolved, terraform plan re-run shows clean output</verify>
  <done>Review complete. Approved or changes requested with specific fixes.</done>
</task>
```

**Pre-Apply Checklist:**
- [ ] `terraform plan` reviewed and approved
- [ ] Correct workspace: `terraform workspace show`
- [ ] State backend is remote (S3 + DynamoDB, GCS)
- [ ] Backup exists for databases being modified
- [ ] Rollback plan documented

---

## CI/CD Pipeline Debugging

```xml
<task type="auto">
  <n>Diagnose pipeline failure in {workflow/step}</n>
  <action>
    1. Get the full error log (not just the summary)
    2. Which step failed and what was it doing?
    3. Is it flaky, environment issue, or code issue?
    4. Did this pipeline pass recently? What changed?
    5. Failing on all branches or just this one?

    Common categories:
    - DEPENDENCY/CACHE: "module not found", version conflicts, works locally
      Fix: clear cache, pin versions, verify lockfile is committed
    - SECRET/ENV: auth failures, 401s, "variable not set"
      Fix: verify secret names match between pipeline YAML and secret store
      Never log secret values — use: echo "Length: ${#SECRET}"
    - FLAKY TESTS: intermittent failures, timing/external services
      Fix: add retry, mock external deps, check race conditions. Document — don't just re-run.
    - RESOURCE/TIMEOUT: OOM killed, disk full, timeout exceeded
      Fix: increase limits, add cleanup steps, optimize slow step
    - PERMISSIONS: "permission denied", RBAC errors
      Fix: check IAM/RBAC on runner service account
  </action>
  <verify>Pipeline passes on test branch before merging to main</verify>
  <done>Pipeline green, fix committed with explanation</done>
</task>
```

---

## GitHub Actions Workflows

```xml
<task type="auto">
  <n>Create GitHub Actions workflow for {purpose}</n>
  <files>.github/workflows/{workflow-name}.yml</files>
  <action>
    Standards:
    1. TRIGGERS: specific branches, use path filters to skip irrelevant changes
    2. STRUCTURE: lint → unit tests → build → integration → deploy
       Set explicit timeouts on all jobs
    3. SECURITY:
       - Pin action versions to SHA: uses: actions/checkout@{sha}
       - GITHUB_TOKEN with minimum required permissions
       - Never echo secrets — use env vars
       - Set permissions block at workflow or job level
    4. CACHING:
       - Cache node_modules/pip/Go modules
       - Content-based cache keys (hash of lockfile)
    5. ARTIFACTS: set retention-days to control storage costs

    Example structure:
    ```yaml
    name: CI
    on:
      push: { branches: [main] }
      pull_request: { branches: [main] }
    permissions:
      contents: read
    jobs:
      test:
        runs-on: ubuntu-latest
        timeout-minutes: 15
        steps:
          - uses: actions/checkout@v4
          - uses: actions/setup-node@v4
            with: { node-version: 20, cache: npm }
          - run: npm ci
          - run: npm test
    ```
  </action>
  <verify>actionlint .github/workflows/{file}</verify>
  <done>Workflow created, validated, and documented</done>
</task>
```

### Useful gh CLI Commands
```bash
gh issue create --title "Bug: ..." --body "..." --label bug
gh pr create --title "feat: ..." --body "..." --base main
gh pr checks
gh release create v1.0.0 --generate-notes
gh run list --workflow=ci.yml --limit=5
```
