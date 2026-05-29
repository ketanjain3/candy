# System Design Document
> `SDD` · Phase 3 — Design · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Mandatory context for all coding agent sessions. Prepend to session or reference via file path in CLAUDE.md.

---

## 1. Architecture Overview

[2–4 sentences describing the overall architecture style — monolith vs. microservices, server-rendered vs. SPA, sync vs. event-driven. State the key architectural trade-offs made and why.]

**Style:** [e.g., Monolithic Next.js app with API routes — chosen for speed of iteration; split if scaling becomes necessary]

---

## 2. Component Breakdown

| Component | Responsibility | Technology |
|-----------|---------------|------------|
| Frontend | [e.g., Server-rendered UI, client-side interactivity] | [e.g., Next.js 14, Tailwind CSS, shadcn/ui] |
| API Layer | [e.g., REST endpoints, business logic, auth middleware] | [e.g., Next.js API Routes / tRPC] |
| Database | [e.g., Persistent data store — users, core entities] | [e.g., PostgreSQL via Supabase] |
| Auth | [e.g., Session management, OAuth, JWT] | [e.g., Supabase Auth / NextAuth.js] |
| File Storage | [e.g., User uploads, static assets] | [e.g., Supabase Storage / S3] |
| Email | [e.g., Transactional email — auth, notifications] | [e.g., Resend / SendGrid] |
| Background Jobs | [e.g., Async processing, scheduled tasks] | [e.g., Inngest / Trigger.dev / cron] |
| Payments | [e.g., Subscription billing] | [e.g., Stripe] |

---

## 3. Architecture Diagram

```
┌──────────────────────────────────────────────────┐
│                   Browser / Client                │
│              (Next.js — React, Tailwind)           │
└──────────────────┬───────────────────────────────┘
                   │ HTTPS
┌──────────────────▼───────────────────────────────┐
│               Next.js App Server                  │
│         (API Routes / Server Actions)             │
│                                                   │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐  │
│  │ Auth       │  │ Core API   │  │ Webhooks   │  │
│  │ Middleware │  │ Handlers   │  │ (Stripe,   │  │
│  └────────────┘  └─────┬──────┘  │  etc.)     │  │
│                         │         └────────────┘  │
└─────────────────────────┼────────────────────────┘
                          │
          ┌───────────────┼─────────────────┐
          │               │                 │
┌─────────▼──────┐ ┌──────▼──────┐ ┌───────▼──────┐
│  PostgreSQL    │ │  Supabase   │ │  External    │
│  (Supabase)   │ │  Storage    │ │  Services    │
│                │ │             │ │  (Stripe,    │
│  - users       │ │  - uploads  │ │   Resend,    │
│  - [entity]    │ │  - assets   │ │   etc.)      │
└────────────────┘ └─────────────┘ └──────────────┘
```

---

## 4. Data Flow

### [Key Flow 1 — e.g., User Sign-Up]

1. User submits form → `POST /api/auth/register`
2. API validates input → creates user in Supabase Auth
3. Supabase triggers → user row created in `users` table
4. Resend sends verification email
5. User redirected to "check your email" page

### [Key Flow 2 — e.g., Core Action]

1. [Step 1]
2. [Step 2]
3. [Step 3]

---

## 5. External Integrations

| Service | Purpose | Auth method | Rate limits / Notes |
|---------|---------|-------------|---------------------|
| [Supabase] | DB, Auth, Storage | Service role key (server-side only) | [Free tier: 500MB DB, 1GB storage] |
| [Stripe] | Payments | Webhook secret + API key | [Test mode in dev; live in prod] |
| [Resend] | Transactional email | API key | [100 emails/day free tier] |
| [Vercel] | Hosting & deployment | CLI / GitHub integration | [Serverless functions; 10s timeout on hobby plan] |

---

## 6. Deployment Topology

| Environment | URL | DB | Notes |
|-------------|-----|----|-------|
| Local dev | `localhost:3000` | Local Supabase / hosted dev project | `.env.local` |
| Staging | [staging URL] | Supabase staging project | Mirrors prod config |
| Production | [prod URL] | Supabase prod project | ENV vars set in Vercel dashboard |

---

## 7. Key Design Decisions

> For full rationale, see `ADR-decisions-log.md`.

- [Decision 1 — e.g., "Using Next.js API routes instead of a separate Express backend → simpler deploy, acceptable for current scale"]
- [Decision 2 — e.g., "Supabase over self-hosted Postgres → faster to start, managed backups, built-in auth"]
- [Decision 3 — e.g., "tRPC for internal type-safe API calls → eliminates request/response type drift between frontend and backend"]
