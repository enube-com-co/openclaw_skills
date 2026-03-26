---
name: terraform-review
description: Review Terraform plans and modules for resource destructions, security misconfigurations, cost risks, reliability gaps, and tagging compliance.
---
# Skill: Terraform Review

## When to use this skill
When reviewing Terraform plans, modules, or IaC changes.

## Terraform Review Task

```xml
<task type="auto">
  <n>Review Terraform plan for {environment/change}</n>
  <action>
    Review the plan output in this order:

    1. DESTRUCTIONS (🔴 always flag)
       - Any resource being destroyed
       - Any resource being replaced (destroy + create)
       - Confirm these are intentional and have a rollback plan

    2. SECURITY (🔴 blockers)
       - Security groups with 0.0.0.0/0 ingress on sensitive ports (22, 3306, 5432, 6379, 27017)
       - IAM policies with * actions or * resources
       - S3 buckets with public access
       - Encryption disabled on RDS, EBS, S3
       - Secrets passed as plain text variables (use AWS SSM / Secrets Manager refs instead)

    3. COST (🟡 warnings)
       - Instance types larger than needed
       - Multi-AZ enabled when not required
       - NAT Gateways (expensive) — confirm necessity
       - Data transfer patterns that will generate large bills

    4. RELIABILITY (🟡 warnings)
       - Single-AZ deployments for production services
       - Missing backup retention on RDS
       - No deletion protection on production databases
       - Missing lifecycle policies on S3 buckets

    5. TAGGING (🟢 suggestions)
       - Required tags present: Environment, Team, CostCenter, ManagedBy
       - Naming conventions followed

    Format findings using the same 🔴/🟡/🟢 system as code reviews.
  </action>
  <verify>All blockers resolved, plan re-run shows clean output</verify>
  <done>Terraform review complete. Approved or changes requested with specific fixes.</done>
</task>
```

## Pre-Apply Checklist

Before running `terraform apply`:
- [ ] `terraform plan` output reviewed and approved
- [ ] Correct workspace selected (`terraform workspace show`)
- [ ] Correct AWS profile / GCP project active
- [ ] State backend is remote (S3 + DynamoDB locking, GCS, etc.)
- [ ] Backup exists for any databases being modified
- [ ] Change window approved (for production)
- [ ] Rollback plan documented

## Module Writing Standards

When writing new Terraform modules:
- Variables must have `description` and `type` — no exceptions
- Sensitive variables must have `sensitive = true`
- Outputs should expose only what callers need
- Include a `README.md` with example usage
- Use `terraform-docs` format for documentation
