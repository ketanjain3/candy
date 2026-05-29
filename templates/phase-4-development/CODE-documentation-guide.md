# Code Documentation Guide
> `CODE` · Phase 4 — Development
>
> **Agent use:** Passively consumed when agents read source files. Follow this guide when generating or reviewing code to keep documentation consistent.

---

## Root README

The project root must have a `README.md` with these sections (and no others):

```markdown
# [Project Name]

[One-sentence description of what it does.]

## Setup

1. Clone the repo
2. `cp .env.example .env.local` and fill in values — see [env var reference](docs/IEC-infra-config.md)
3. `npm install`
4. `npm run db:migrate`
5. `npm run dev` → http://localhost:3000

## Running Tests

`npm test`

## Project Structure

[Brief directory map — 8 lines max]

## Docs

- Architecture: `docs/SDD-system-design.md`
- Requirements: `docs/SRS-software-requirements.md`
- API: `docs/ADD-api-design.md`
- Decisions: `docs/ADR-decisions-log.md`
```

---

## Module-Level READMEs

Add a `README.md` inside any directory that is not self-explanatory from its name alone.

**Required for:**
- `src/server/` — explain what lives here vs. `src/lib/`
- `src/components/` — explain the `ui/` vs feature component distinction
- Any directory with > 6 files and a non-obvious purpose

**Format:** 5–10 lines maximum. What does this module do? What goes here vs. somewhere else?

---

## Function / Method Docstrings

**When to write:** Only when the function's behavior is not obvious from its name and types alone.

**What to include:** The non-obvious behavior, a side effect, a constraint, or a "why" that the code cannot express.

**What NOT to include:** Restating the parameter names. What the function does if it's already clear from the name.

**Format (TypeScript/JavaScript):**
```typescript
/**
 * Returns paginated results. Caller is responsible for sanitizing `cursor` —
 * it is interpolated directly into the query predicate.
 */
async function getItems(userId: string, cursor?: string, limit = 20) {
  // ...
}
```

**Format (Python):**
```python
def get_items(user_id: str, cursor: str | None = None, limit: int = 20) -> list[Item]:
    """
    Returns paginated items for the given user.
    cursor is opaque — pass the value from the previous response's next_cursor field.
    """
```

---

## Inline Comments

Write a comment when:
- There is a hidden constraint or invariant that would surprise a future reader
- The code is a workaround for a specific bug in a library or browser
- An unusual approach was chosen and the reason must be preserved

Do NOT write a comment when:
- The code is self-explanatory from well-named identifiers
- The comment just restates what the code does in English

**Good:**
```typescript
// Stripe requires idempotency key to be unique per request, not per user —
// using requestId, not userId, to prevent retry collisions across sessions
await stripe.charges.create(params, { idempotencyKey: requestId });
```

**Bad:**
```typescript
// Create a Stripe charge
await stripe.charges.create(params);
```

---

## What to Document vs. What to Skip

| Item | Document? | Format |
|------|-----------|--------|
| Public API functions | Yes, if non-obvious | JSDoc/docstring |
| Internal utility functions | Only if tricky | Inline comment |
| React components (props) | If prop types are not self-evident | JSDoc on the props type |
| Environment variables | Yes — always | `.env.example` with comment per var |
| Database schema | Yes | In `DBD-database-design.md` |
| Non-obvious SQL / queries | Yes | Inline comment above the query |
| Test cases | Only if the scenario is non-obvious | Comment above the `it()` block |
| Config files | Only unusual values | Inline comment |

---

## .env.example Template

```bash
# ─── App ──────────────────────────────────────────
NEXT_PUBLIC_APP_URL=http://localhost:3000

# ─── Database ─────────────────────────────────────
DATABASE_URL=                    # PostgreSQL connection string
DIRECT_URL=                      # Direct (non-pooled) connection — for migrations

# ─── Auth ─────────────────────────────────────────
NEXTAUTH_SECRET=                 # Random 32-char string: openssl rand -base64 32
NEXTAUTH_URL=http://localhost:3000

# ─── Supabase ─────────────────────────────────────
NEXT_PUBLIC_SUPABASE_URL=        # Project URL from Supabase dashboard
NEXT_PUBLIC_SUPABASE_ANON_KEY=   # Anon/public key — safe to expose
SUPABASE_SERVICE_ROLE_KEY=       # Service role key — server-side only, never expose

# ─── Email ────────────────────────────────────────
RESEND_API_KEY=                  # From Resend dashboard

# ─── Payments ─────────────────────────────────────
STRIPE_SECRET_KEY=               # From Stripe dashboard (sk_test_ in dev)
STRIPE_WEBHOOK_SECRET=           # From Stripe webhook config (whsec_...)
NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY=  # From Stripe dashboard (pk_test_ in dev)
```
