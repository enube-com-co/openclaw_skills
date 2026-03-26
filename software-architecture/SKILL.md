---
name: software-architecture
description: Review system architecture against SOLID principles, clean architecture layers, and design patterns; flag violations and document decisions with ADRs.
---
# Skill: Software Architecture

## When to use this skill
When designing system architecture, evaluating design patterns, or reviewing structural decisions.

## Architecture Review Task

```xml
<task type="auto">
  <n>Architecture review for {component/system}</n>
  <action>
    Evaluate against these principles (in priority order):

    1. SOLID PRINCIPLES
       - Single Responsibility: each module/class has one reason to change
       - Open/Closed: open for extension, closed for modification
       - Liskov Substitution: subtypes must be substitutable for base types
       - Interface Segregation: no client forced to depend on unused interfaces
       - Dependency Inversion: depend on abstractions, not concretions

    2. CLEAN ARCHITECTURE LAYERS
       - Entities: business rules (no framework dependencies)
       - Use Cases: application-specific business rules
       - Interface Adapters: convert data between use cases and external
       - Frameworks & Drivers: web, DB, UI (outermost, most volatile)
       - Dependencies point INWARD only

    3. DESIGN PATTERNS (apply when appropriate)
       - Repository Pattern: abstract data access
       - Factory Pattern: encapsulate object creation
       - Strategy Pattern: encapsulate interchangeable algorithms
       - Observer Pattern: decouple event producers from consumers
       - Adapter Pattern: bridge incompatible interfaces

    4. ANTI-PATTERNS TO FLAG
       - God classes/modules (>500 lines, too many responsibilities)
       - Circular dependencies between modules
       - Leaky abstractions (implementation details in interfaces)
       - Tight coupling to specific frameworks/vendors
       - Premature optimization masking readability

    Format findings as:
    - 🔴 VIOLATION: {principle violated} — {where} — {suggested fix}
    - 🟡 CONCERN: {potential issue} — {risk} — {recommendation}
    - 🟢 GOOD: {well-applied pattern} — {why it works here}
  </action>
  <verify>All violations addressed, architecture supports stated requirements</verify>
  <done>Architecture review complete with actionable recommendations</done>
</task>
```

## Architecture Decision Record (ADR) Template

When making significant architecture decisions, document them:

```markdown
# ADR-{number}: {title}

## Status: proposed / accepted / deprecated / superseded

## Context
What is the problem or situation that requires a decision?

## Decision
What is the change we're making?

## Consequences
What are the positive, negative, and neutral effects?

## Alternatives Considered
What other options were evaluated and why were they rejected?
```
