# Infrastructure & Environment Config
> `IEC` · Phase 6 — Deployment · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Attach when working with an agent on infra, debugging, or onboarding a new environment.

---

## Tech Stack Summary

| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| Frontend framework | [Next.js] | [14.x] | App Router |
| Language | [TypeScript] | [5.x] | Strict mode |
| UI components | [shadcn/ui + Tailwind CSS] | [latest] | |
| Database | [PostgreSQL] | [15] | Hosted via Supabase |
| ORM | [Prisma] | [5.x] | |
| Auth | [Supabase Auth / NextAuth.js] | [—] | |
| File storage | [Supabase Storage] | [—] | |
| Hosting | [Vercel] | [—] | Serverless; 10s fn timeout on hobby |
| Email | [Resend] | [—] | |
| Payments | [Stripe] | [—] | |
| Background jobs | [Inngest / Trigger.dev / none] | [—] | |
| CI/CD | [GitHub Actions / Vercel auto-deploy] | [—] | |
| Monitoring | [Vercel analytics / Sentry] | [—] | |

---

## Environment Variables

> Values are NEVER stored here. This is a reference for what each variable does and how to obtain it.
> Actual values live in `.env.local` (local) and the Vercel dashboard (staging/prod).

See `.env.example` in the project root for the complete list with inline comments.

| Variable | Required | Scope | How to obtain |
|----------|----------|-------|---------------|
| `NEXT_PUBLIC_APP_URL` | Yes | Client + Server | Set to `http://localhost:3000` locally, production URL in prod |
| `DATABASE_URL` | Yes | Server | Supabase → Settings → Database → Connection string (pooled) |
| `DIRECT_URL` | Yes | Server | Supabase → Settings → Database → Connection string (direct) — used by Prisma migrate |
| `NEXTAUTH_SECRET` | Yes | Server | `openssl rand -base64 32` |
| `NEXTAUTH_URL` | Yes | Server | Same as `NEXT_PUBLIC_APP_URL` |
| `NEXT_PUBLIC_SUPABASE_URL` | Yes | Client + Server | Supabase → Settings → API → Project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Yes | Client + Server | Supabase → Settings → API → anon/public key |
| `SUPABASE_SERVICE_ROLE_KEY` | Yes | Server only | Supabase → Settings → API → service_role key |
| `RESEND_API_KEY` | Yes | Server | Resend dashboard → API Keys |
| `STRIPE_SECRET_KEY` | Yes | Server | Stripe dashboard → Developers → API keys (use `sk_test_` in dev) |
| `STRIPE_WEBHOOK_SECRET` | Yes | Server | Stripe → Webhooks → Signing secret (`whsec_...`) |
| `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` | Yes | Client | Stripe dashboard → Developers → API keys (use `pk_test_` in dev) |

---

## Service Dependencies

| Service | Purpose | Free tier limits | Account needed |
|---------|---------|-----------------|----------------|
| Supabase | Database, Auth, Storage | 500MB DB, 1GB storage, 50MB file uploads | [supabase.com] |
| Vercel | Hosting, serverless functions | 100GB bandwidth, 10s fn timeout | [vercel.com] |
| Resend | Transactional email | 100 emails/day, 3,000/month | [resend.com] |
| Stripe | Payments | No monthly fee; 2.9% + 30¢/transaction | [stripe.com] |
| GitHub | Source control, CI/CD | Free for public/private repos | [github.com] |

---

## Ports & Local Services

| Service | Port | Notes |
|---------|------|-------|
| Next.js dev server | `3000` | `npm run dev` |
| Supabase local (if used) | `54321` | `supabase start` |
| Supabase local DB | `54322` | Direct Postgres connection |
| [Other local service] | [port] | [notes] |

---

## Reproducing a Fresh Environment

```bash
# 1. Clone the repo
git clone [repo-url]
cd [project-name]

# 2. Install dependencies
npm install

# 3. Configure environment
cp .env.example .env.local
# Fill in all values — see table above for how to obtain each

# 4. Apply database schema
npm run db:migrate

# 5. Seed with dev data (optional)
npm run db:seed

# 6. Start the dev server
npm run dev
# → http://localhost:3000
```
