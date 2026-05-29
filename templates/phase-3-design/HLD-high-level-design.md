# High-Level Design
> `HLD` · Phase 3 — Design · Last updated: [YYYY-MM-DD]
>
> **Purpose:** Conceptual blueprint of the system — how major components relate, how data moves, and why the architecture is shaped the way it is. Sits above SDD in abstraction; no implementation detail.
>
> **Agent use:** Attach at the start of any architecture-level session to give the agent a mental model of the whole system before diving into a component.

---

## 1. System Context

> Who/what interacts with this system from the outside?

```
                    ┌─────────────────────────────┐
   [Browser User] ──►                             │
                    │       [Product Name]         │
   [Mobile User]  ──►                             ◄── [External Service A]
                    │                             │
   [Admin User]   ──►                             ◄── [External Service B]
                    └─────────────────────────────┘
                                  │
                            [Data Store]
```

- **Users:** [Who accesses the system and how — browser, API, CLI, etc.]
- **External systems:** [Third-party services the system depends on or exposes data to]
- **Data store:** [Primary persistence layer]

---

## 2. Architectural Style

**Pattern:** [e.g., Monolithic · Modular Monolith · Microservices · Serverless · Event-driven]

**Why this pattern:**
[2–3 sentences. What properties of this pattern fit the current constraints — team size, scale expectations, deploy simplicity?]

**Key trade-offs accepted:**
- [Trade-off 1 — e.g., "Monolith limits independent scaling of components — acceptable until user growth demands it"]
- [Trade-off 2]

---

## 3. Major Components

> One row per top-level system boundary. Not every module — just the major logical groupings.

| Component | Responsibility | Owned by |
|-----------|---------------|----------|
| [Frontend] | [Render UI, handle client state, call API] | [Next.js app] |
| [API / Backend] | [Business logic, data access, auth enforcement] | [Next.js API routes / Express / FastAPI] |
| [Auth Service] | [Identity, session management, token issuance] | [Supabase Auth / NextAuth] |
| [Data Store] | [Persistent storage — relational + blob] | [PostgreSQL + Object Storage] |
| [Job Queue] | [Async processing — emails, webhooks, background tasks] | [Inngest / Trigger.dev / BullMQ] |
| [CDN / Edge] | [Static asset delivery, edge caching] | [Vercel Edge / Cloudflare] |

---

## 4. Component Interaction Diagram

```
┌──────────────┐       HTTPS        ┌──────────────────────┐
│   Frontend   │ ◄────────────────► │   API / Backend      │
│  (Browser)   │                    │                      │
└──────────────┘                    │  ┌────────────────┐  │
                                    │  │  Auth Module   │  │
                                    │  └───────┬────────┘  │
                                    │          │            │
                                    │  ┌───────▼────────┐  │
                                    │  │ Business Logic │  │
                                    │  └───────┬────────┘  │
                                    │          │            │
                                    └──────────┼────────────┘
                                               │
                    ┌──────────────────────────┼──────────────┐
                    │                          │              │
             ┌──────▼──────┐         ┌─────────▼──────┐  ┌───▼──────┐
             │  PostgreSQL  │         │ Object Storage │  │ Job Queue│
             └─────────────┘         └────────────────┘  └──────────┘
```

---

## 5. Data Flow — Key Scenarios

### [Scenario 1 — e.g., Authenticated Request]

```
Browser → API (with JWT) → Auth Module (validate token)
       → Business Logic → DB Query → Response
```

### [Scenario 2 — e.g., Background Job]

```
API Handler → Enqueue Job → Return 202
                ↓ (async)
           Job Worker → External Service → Update DB → Send notification
```

### [Scenario 3 — e.g., Webhook Ingestion]

```
External Service → POST /webhooks/[service]
                → Validate signature
                → Enqueue for async processing
                → Return 200
                ↓ (async)
           Process event → Update DB state
```

---

## 6. Cross-Cutting Concerns

| Concern | Approach |
|---------|---------|
| Authentication | [e.g., JWT validated on every protected route via middleware] |
| Authorization | [e.g., Row-level security in DB + role check in API handlers] |
| Logging | [e.g., Structured JSON logs via `pino`; forwarded to Vercel log drain] |
| Error handling | [e.g., Global error boundary in frontend; standardized error envelope in API] |
| Rate limiting | [e.g., Upstash Redis rate limiter on API routes] |
| Caching | [e.g., SWR on client; no server-side cache in v1] |
| Secrets management | [e.g., Env vars in Vercel dashboard; never committed to repo] |

---

## 7. Scalability Considerations

> Note what will break first as load grows, and what the upgrade path is.

| Bottleneck | Threshold | Mitigation path |
|------------|-----------|----------------|
| [DB connection pool] | [~100 concurrent users] | [Switch to PgBouncer / Supabase connection pooling] |
| [Serverless cold starts] | [If latency becomes noticeable] | [Edge functions for hot paths / keep-warm strategy] |
| [Single-region DB] | [If global latency matters] | [Supabase read replicas or move to distributed DB] |
| [Monolith deploy] | [If team grows past 1–2] | [Extract high-churn services into separate deployments] |

---

## 8. Security Boundaries

```
PUBLIC (no auth required)
  └── Landing page, sign-up, login, password reset, /webhooks/*

AUTHENTICATED (valid session required)
  └── All /api/* routes except the above
  └── All app pages except landing / auth flows

ADMIN ONLY
  └── /api/admin/* — role check: user.role === 'admin'
  └── Direct DB access — never exposed via API
```
