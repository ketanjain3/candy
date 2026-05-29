# Software Requirements Specification
> `SRS` · Phase 2 — Requirements · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Primary context file for all coding agent sessions. Attach in full.

---

## 1. Overview

- **Product:** [Product name]
- **Version:** [v1.0 / MVP]
- **Description:** [1–2 sentence product description]

---

## 2. Functional Requirements

> Priority: **Must** = required for launch · **Should** = high value, best effort · **Could** = nice to have

### 2.1 Authentication & Identity

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-01 | [e.g., "User can sign up with email and password"] | Must | [e.g., "Given valid email + password → account created, verification email sent"] |
| FR-02 | [e.g., "User can log in with OAuth (Google)"] | Must | [e.g., "OAuth flow completes → user session created → redirect to dashboard"] |
| FR-03 | [e.g., "User can reset password via email link"] | Must | [e.g., "Reset link expires in 1 hour; new password accepted on submit"] |

### 2.2 [Core Feature Area — e.g., Dashboard]

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-10 | [Requirement] | Must | [Acceptance criteria] |
| FR-11 | [Requirement] | Should | [Acceptance criteria] |
| FR-12 | [Requirement] | Could | [Acceptance criteria] |

### 2.3 [Core Feature Area 2]

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-20 | [Requirement] | Must | [Acceptance criteria] |
| FR-21 | [Requirement] | Must | [Acceptance criteria] |

### 2.4 [Core Feature Area 3]

| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-30 | [Requirement] | Should | [Acceptance criteria] |

---

## 3. Non-Functional Requirements

| ID | Category | Requirement | Priority |
|----|----------|-------------|----------|
| NFR-01 | Performance | [e.g., "API responses must complete in < 500ms at p95 under normal load"] | Must |
| NFR-02 | Performance | [e.g., "Page load (LCP) must be < 2.5 seconds on desktop broadband"] | Must |
| NFR-03 | Security | [e.g., "All API endpoints must require authentication unless explicitly public"] | Must |
| NFR-04 | Security | [e.g., "Passwords must be hashed with bcrypt, min cost factor 12"] | Must |
| NFR-05 | Security | [e.g., "All data in transit must use TLS 1.2+"] | Must |
| NFR-06 | Availability | [e.g., "Target 99.5% uptime — planned maintenance excluded"] | Should |
| NFR-07 | Scalability | [e.g., "System must support [N] concurrent users without degradation"] | Should |
| NFR-08 | Maintainability | [e.g., "Test coverage must be > 80% for all business logic modules"] | Should |
| NFR-09 | Accessibility | [e.g., "UI must meet WCAG 2.1 AA standard"] | Should |
| NFR-10 | Browser support | [e.g., "Must support Chrome, Firefox, Safari — latest 2 versions"] | Must |

---

## 4. Data Requirements

| Entity | Description | Key Fields | Retention |
|--------|-------------|------------|-----------|
| [User] | [e.g., "Registered account holder"] | [id, email, created_at, role] | [Indefinite / until deleted] |
| [Entity 2] | [Description] | [Fields] | [Retention policy] |
| [Entity 3] | [Description] | [Fields] | [Retention policy] |

---

## 5. External Interface Requirements

| System | Direction | Protocol | Purpose |
|--------|-----------|----------|---------|
| [e.g., Stripe] | Outbound | REST/HTTPS | [e.g., Payment processing and subscription management] |
| [e.g., SendGrid] | Outbound | REST/HTTPS | [e.g., Transactional email delivery] |
| [e.g., Google OAuth] | Inbound | OAuth 2.0 | [e.g., Social login] |

---

## 6. System Constraints

- **Platform:** [e.g., Web only — no native mobile app in v1]
- **Language/Runtime:** [e.g., TypeScript / Node.js 20+]
- **Framework:** [e.g., Next.js 14 with App Router]
- **Hosting:** [e.g., Vercel for frontend, Railway for backend]
- **Database:** [e.g., PostgreSQL via Supabase]
- **Auth:** [e.g., Supabase Auth or NextAuth.js]
- **Third-party budget:** [e.g., Free tier only for all external services in v1]
