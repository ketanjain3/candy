# Database Design Document
> `DBD` · Phase 3 — Design · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Always attach when an agent session involves any database layer work.

---

## 1. Overview

- **Database:** [e.g., PostgreSQL 15 via Supabase]
- **ORM / Query layer:** [e.g., Prisma / Drizzle / raw SQL]
- **Schema file location:** [`prisma/schema.prisma` or `src/db/schema.ts`]

---

## 2. Entity Definitions

### `users`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | `uuid` | PK, default `gen_random_uuid()` | Primary key |
| `email` | `text` | NOT NULL, UNIQUE | User's email address |
| `display_name` | `text` | NULLABLE | User-facing display name |
| `role` | `text` | NOT NULL, default `'user'` | `user` or `admin` |
| `created_at` | `timestamptz` | NOT NULL, default `now()` | Account creation time |
| `updated_at` | `timestamptz` | NOT NULL, default `now()` | Last update time |

### `[entity_name]`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | `uuid` | PK, default `gen_random_uuid()` | Primary key |
| `user_id` | `uuid` | FK → `users.id`, ON DELETE CASCADE | Owning user |
| `[field]` | `[type]` | [constraints] | [description] |
| `created_at` | `timestamptz` | NOT NULL, default `now()` | |
| `updated_at` | `timestamptz` | NOT NULL, default `now()` | |

### `[entity_name_2]`

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| `id` | `uuid` | PK, default `gen_random_uuid()` | |
| `[field]` | `[type]` | [constraints] | [description] |

---

## 3. Relationships

| Relationship | Type | Description |
|-------------|------|-------------|
| `users` → `[entity]` | One-to-many | Each user owns many [entities]; deleting a user cascades |
| `[entity]` → `[entity_2]` | Many-to-many | Via join table `[entity_entity_2]` |
| `[entity]` → `[entity_3]` | One-to-one | Each [entity] has exactly one [entity_3] |

---

## 4. ER Diagram

```
users
  │
  │ 1:N
  ▼
[entity]          [entity_2]
  │                   │
  │ N:M (join table)  │
  └───────────────────┘
```

---

## 5. Indexes

| Table | Column(s) | Index type | Reason |
|-------|-----------|------------|--------|
| `users` | `email` | UNIQUE | Login lookup |
| `[entity]` | `user_id` | B-tree | Filter by owner — high frequency |
| `[entity]` | `created_at DESC` | B-tree | Pagination — default sort |
| `[entity]` | `(user_id, status)` | Composite | Filtered list queries |

---

## 6. Row-Level Security (if using Supabase)

| Table | Policy | Rule |
|-------|--------|------|
| `[entity]` | `SELECT` | `auth.uid() = user_id` |
| `[entity]` | `INSERT` | `auth.uid() = user_id` |
| `[entity]` | `UPDATE` | `auth.uid() = user_id` |
| `[entity]` | `DELETE` | `auth.uid() = user_id` |

---

## 7. Migration Strategy

- **Tool:** [e.g., Prisma Migrate / Supabase migrations / Flyway]
- **Location:** [`prisma/migrations/` or `supabase/migrations/`]
- **Process:** All schema changes go through migration files — never modify the DB directly in production
- **Naming convention:** `[timestamp]_[description].sql` (e.g., `20240601_add_status_to_entity.sql`)
- **Rollback:** [e.g., Write a corresponding down migration for every up migration]

---

## 8. Seed Data

- **Dev seed file:** [`prisma/seed.ts` or `scripts/seed.sql`]
- **Contents:** [e.g., 1 admin user, 3 sample users, 10 sample [entities]]
- **Run with:** `[npm run db:seed / supabase db reset]`
