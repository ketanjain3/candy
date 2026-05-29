# CLAUDE.md — Agent Instruction File
> `CLAUDE.md` · Phase 4 — Development
>
> **How to use:** Copy this file to the root of your project as `CLAUDE.md`. Claude Code auto-loads it. Fill in each section as your project takes shape. Start minimal; add rules as agents make recurring mistakes.

---

## Project Overview

- **Product:** [Product name — one sentence description]
- **Stack:** [e.g., Next.js 14 / TypeScript / PostgreSQL / Tailwind CSS / Supabase]
- **Docs:** SRS: `docs/SRS.md` · SDD: `docs/SDD.md` · ADR: `docs/ADR.md`

---

## Codebase Structure

```
[project-root]/
├── src/
│   ├── app/              # Next.js App Router pages and layouts
│   ├── components/       # Shared React components
│   │   ├── ui/           # Base UI components (shadcn — do not modify directly)
│   │   └── [feature]/    # Feature-specific components
│   ├── lib/              # Utility functions, shared logic
│   ├── server/           # Server-side logic, DB queries, service layer
│   ├── hooks/            # Custom React hooks
│   └── types/            # Shared TypeScript types and interfaces
├── prisma/               # Prisma schema and migrations
├── public/               # Static assets
├── docs/                 # Project documentation
└── tests/                # Test files
```

---

## How to Run

```bash
# Install dependencies
npm install

# Start local dev server
npm run dev

# Run tests
npm test

# Run database migrations
npm run db:migrate

# Seed the database
npm run db:seed
```

---

## Environment Setup

Copy `.env.example` → `.env.local` and fill in all values before running locally.
See `docs/IEC-infra-config.md` for required services and how to obtain credentials.

---

## Coding Conventions

- **Language:** TypeScript — strict mode on. No `any` without a comment explaining why.
- **Formatting:** Prettier (runs on save). Do not reformat unrelated code in a PR.
- **Naming:** `camelCase` for variables/functions, `PascalCase` for components/types, `SCREAMING_SNAKE_CASE` for env vars and constants.
- **Components:** One component per file. No default exports in `lib/` or `server/` — named exports only.
- **Imports:** Use path aliases (`@/components/...`) not relative deep imports (`../../../`).
- **Comments:** Only when the WHY is non-obvious. No commented-out code committed.

---

## What NOT to Modify

- `src/components/ui/` — shadcn/ui generated components. Extend via wrapper components, not direct edits.
- `prisma/migrations/` — never edit existing migration files. Create new ones.
- [Any other file or directory to protect]

---

## Database Rules

- All schema changes go through Prisma migrations. Never modify the DB directly.
- Sensitive operations (deletes, bulk updates) must use transactions.
- Do not add N+1 queries — use `include` or batch queries.
- RLS is enforced at the Supabase layer. Do not bypass it server-side without explicit approval.

---

## Testing Approach

- **Unit tests:** [e.g., Vitest] for pure functions in `lib/` and `server/`
- **Integration tests:** [e.g., Vitest + test DB] for API routes and server actions
- **E2E tests:** [e.g., Playwright] for critical user flows only
- Tests live in `tests/` mirroring `src/` structure
- Run `npm test` before considering any task complete

---

## Agent Pitfalls to Avoid

- Do not create new npm packages without checking if the functionality already exists in the project
- Do not add `console.log` statements — use the logger at `src/lib/logger.ts`
- Do not store secrets in code — use env vars only
- Do not modify `package-lock.json` manually — always use `npm install`
- Do not use `any` in TypeScript — if you must, leave a comment explaining why
- Do not write raw SQL unless using the established query pattern in `src/server/db.ts`
- [Add more as patterns emerge]
