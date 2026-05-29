# Deployment Runbook
> `DP` · Phase 6 — Deployment · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Attach to any DevOps or infrastructure agent session. Agents can help update this as the deploy process evolves.

---

## Environments

| Environment | URL | Branch | Deploy trigger |
|-------------|-----|--------|----------------|
| Production | [https://your-domain.com] | `main` | Merge to `main` → auto-deploy |
| Staging | [https://staging.your-domain.com] | `staging` | Merge to `staging` → auto-deploy |
| Local dev | `http://localhost:3000` | any | `npm run dev` |

---

## Pre-Deploy Checklist

Before merging to `main`:

- [ ] All tests pass locally (`npm test`)
- [ ] No TypeScript errors (`npm run typecheck`)
- [ ] No linting errors (`npm run lint`)
- [ ] New env vars are added to Vercel dashboard (prod + staging)
- [ ] New env vars are added to `.env.example` with a comment
- [ ] Database migrations are checked in and tested on staging
- [ ] Feature tested end-to-end on staging
- [ ] `KNOWN_ISSUES.md` updated if any known issues ship with this change

---

## Deploy Steps (Standard)

> CI/CD is fully automated on merge to `main`. These steps are for manual deploys or verification.

1. Merge PR to `main` via GitHub
2. Vercel auto-deploy triggers — monitor at [Vercel dashboard URL]
3. Wait for build to complete (typically [X] minutes)
4. Run post-deploy verification (see below)

### Manual Deploy (if needed)

```bash
# Push to trigger deploy
git push origin main

# Or force a redeploy via Vercel CLI
vercel --prod
```

---

## Database Migrations

Run migrations **before** deploying new code that depends on them.

```bash
# Apply pending migrations (staging)
DATABASE_URL=<staging-url> npx prisma migrate deploy

# Apply pending migrations (production)
DATABASE_URL=<prod-url> npx prisma migrate deploy
```

> If a migration fails in production, do NOT apply new code — rollback and investigate first.

---

## Post-Deploy Verification

After every production deploy:

- [ ] Home page loads without error
- [ ] User can log in (test with a known account)
- [ ] Core action works end-to-end: [describe the most critical user flow]
- [ ] No errors in [Vercel / Railway / hosting platform] logs in the first 5 minutes
- [ ] [Any other smoke test specific to the feature deployed]

---

## Rollback Steps

### Option A — Revert via Vercel (instant, no code change)

1. Open Vercel dashboard → Deployments
2. Find the last known-good deployment
3. Click "Promote to Production"
4. Verify post-deploy checks pass

### Option B — Revert via Git

```bash
# Revert the merge commit
git revert -m 1 <merge-commit-sha>
git push origin main
```

### Option C — Database Rollback (destructive — use only if necessary)

```bash
# Roll back the last migration
DATABASE_URL=<prod-url> npx prisma migrate resolve --rolled-back <migration-name>
```

> Database rollbacks can cause data loss. Only use if the migration is actively breaking production and there is no forward-fix available.

---

## Environment-Specific Notes

### Production

- Hosted on: [Vercel / Railway / Fly.io / etc.]
- Database: [Supabase prod project — URL in Vercel env vars]
- Logs: [Vercel dashboard → Functions tab / Datadog / etc.]
- Alerts: [Where are alerts sent? PagerDuty / email / Slack?]

### Staging

- Mirrors production config exactly
- Data is not real — safe to delete / reset
- Reset staging DB: `supabase db reset --db-url <staging-url>`
