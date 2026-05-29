# Business Requirements Document
> `BRD` · Phase 2 — Requirements · Last updated: [YYYY-MM-DD]

---

## User Personas

### Persona 1 — [Name / Role]

- **Who:** [Brief description — job title, company size, technical level]
- **Goal:** [What outcome are they trying to achieve?]
- **Pain point:** [What friction do they face today?]
- **Success looks like:** [How do they know the product worked?]

### Persona 2 — [Name / Role]

- **Who:** [Brief description]
- **Goal:** [Outcome they want]
- **Pain point:** [Current friction]
- **Success looks like:** [Definition of success]

---

## Business Rules

[Business rules are hard constraints the product must enforce — not features, not UX decisions. If violated, the product is broken or non-compliant.]

| ID | Rule | Rationale |
|----|------|-----------|
| BR-01 | [e.g., "A user may not belong to more than one active organization"] | [Why this rule exists] |
| BR-02 | [Rule] | [Rationale] |
| BR-03 | [Rule] | [Rationale] |

---

## In-Scope Features (v1)

[These are the features that will be built. Everything else is out of scope by default.]

- [Feature 1 — one sentence description]
- [Feature 2]
- [Feature 3]
- [Feature 4]

---

## Explicit Out-of-Scope (v1)

[List things that might seem in-scope but are not. Being explicit here prevents scope creep and keeps agents focused.]

- [Out-of-scope item 1 — e.g., "Multi-tenancy / team workspaces"]
- [Out-of-scope item 2 — e.g., "Mobile app"]
- [Out-of-scope item 3]

---

## Constraints

| Type | Constraint |
|------|-----------|
| Regulatory | [e.g., "Must not store PII without explicit user consent (GDPR)"] |
| Security | [e.g., "All data must be encrypted at rest and in transit"] |
| Performance | [e.g., "Core action must complete in under 2 seconds"] |
| Platform | [e.g., "Must run entirely in browser — no desktop app"] |
| Integration | [e.g., "Must integrate with Stripe for billing"] |

---

## Assumptions

[Things believed to be true that have not been validated. If wrong, requirements may need to change.]

1. [Assumption — e.g., "Users are comfortable with OAuth-based login"]
2. [Assumption]
3. [Assumption]
