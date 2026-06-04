# Low-Level Design
> `LLD` · Phase 3 — Design · Last updated: [YYYY-MM-DD]
>
> **Purpose:** Implementation-level design for a specific module or feature. Defines function signatures, data structures, algorithms, state machines, and error handling contracts before code is written. One LLD per significant feature or module — not one for the whole system.
>
> **Agent use:** Attach to the coding agent session for the specific module being implemented. The agent should produce code that matches these contracts exactly — treat mismatches as bugs, not suggestions.

---

## Module / Feature

**Name:** [e.g., User Authentication Module / Item CRUD Service / Payment Webhook Handler]
**Scope:** [What this LLD covers — 1 sentence]
**Related docs:** SRS requirements: [FR-01, FR-02] · SDD component: [Auth Module] · API endpoints: [POST /auth/login]

---

## 1. Module Responsibilities

- [Responsibility 1 — e.g., "Validate user credentials against the DB"]
- [Responsibility 2 — e.g., "Issue a signed JWT on successful auth"]
- [Responsibility 3 — e.g., "Enforce account lockout after N failed attempts"]

**Explicitly out of scope for this module:**
- [e.g., "Password reset — handled by `PasswordResetService`"]
- [e.g., "OAuth flows — handled by `OAuthService`"]

---

## 2. Data Structures

### Input Types

```typescript
// [Name of input type]
type LoginInput = {
  email: string        // validated: non-empty, valid email format
  password: string     // validated: non-empty, min 8 chars
}

// [Name of another input type]
type [EntityInput] = {
  [field]: [type]      // [validation rule]
  [field]: [type]      // [validation rule]
}
```

### Output Types

```typescript
// Success response
type AuthResult = {
  token: string        // signed JWT, expires in 7 days
  user: {
    id: string
    email: string
    role: 'user' | 'admin'
  }
}

// Error response
type AuthError =
  | { code: 'INVALID_CREDENTIALS' }
  | { code: 'ACCOUNT_LOCKED'; unlocksAt: Date }
  | { code: 'VALIDATION_ERROR'; fields: Record<string, string> }
```

### Internal / Intermediate Types

```typescript
// [Name — e.g., stored session record]
type FailedAttemptRecord = {
  userId: string
  count: number
  lastAttemptAt: Date
}
```

---

## 3. Function / Method Signatures

> Define the public interface of this module. Implementations must match these signatures.

```typescript
// Validate credentials and return an auth token or throw an AuthError
async function login(input: LoginInput): Promise<AuthResult>

// Check if the account is currently locked; return unlock time if so
async function getAccountLockStatus(userId: string): Promise<{ locked: boolean; unlocksAt?: Date }>

// Record a failed attempt; lock account if threshold is exceeded
async function recordFailedAttempt(userId: string): Promise<void>

// Clear failed attempt counter on successful login
async function clearFailedAttempts(userId: string): Promise<void>

// Verify a JWT token and return the decoded payload, or null if invalid/expired
function verifyToken(token: string): TokenPayload | null
```

---

## 4. Algorithms & Logic

### [Algorithm 1 — e.g., Login Flow]

```
login(input):
  1. Validate input schema → throw VALIDATION_ERROR if invalid
  2. Look up user by email → throw INVALID_CREDENTIALS if not found
                             (same error as wrong password — no user enumeration)
  3. Check account lock status → throw ACCOUNT_LOCKED if locked
  4. Verify password hash (bcrypt.compare) → if fail:
       a. recordFailedAttempt(userId)
       b. throw INVALID_CREDENTIALS
  5. clearFailedAttempts(userId)
  6. Sign JWT with { userId, email, role }, expiry = 7d
  7. Return AuthResult
```

### [Algorithm 2 — e.g., Account Lockout]

```
recordFailedAttempt(userId):
  1. Increment failed_attempts counter in cache/DB (key: `lock:{userId}`)
  2. Set TTL = 15 minutes (resets window on each failure)
  3. If counter >= LOCKOUT_THRESHOLD (5):
       a. Set `locked_until` = now + 15 minutes
       b. (Counter continues to exist but lock check happens first)
```

### [Algorithm 3 — e.g., Token Verification]

```
verifyToken(token):
  1. jwt.verify(token, JWT_SECRET)
  2. If expired or invalid signature → return null
  3. Check token not in denylist (if revocation is implemented)
  4. Return decoded payload
```

---

## 5. State Machine (if applicable)

> Use for entities or flows that have meaningful state transitions.

```
[Entity / Flow]: [e.g., User Account Status]

         ┌─────────────────────────────────────┐
         │                                     │
  [UNVERIFIED] ──verify email──► [ACTIVE] ──5 failures──► [LOCKED]
       │                            │                         │
   delete                        delete                  15min TTL
       │                            │                         │
       ▼                            ▼                         ▼
  [DELETED]                   [DELETED]                  [ACTIVE]
```

**States:**
| State | Meaning | Transitions out |
|-------|---------|----------------|
| `UNVERIFIED` | Account created, email not confirmed | → `ACTIVE` on email verify |
| `ACTIVE` | Normal usable account | → `LOCKED` on 5 failures; → `DELETED` on delete request |
| `LOCKED` | Temporarily blocked | → `ACTIVE` after 15 minutes |
| `DELETED` | Soft-deleted, data retained 30 days | → [permanent deletion via cron] |

---

## 6. Error Handling

| Error scenario | Error code | HTTP status | Logged? | Notes |
|----------------|------------|-------------|---------|-------|
| Email not found | `INVALID_CREDENTIALS` | 401 | No | Same response as wrong password — prevent user enumeration |
| Wrong password | `INVALID_CREDENTIALS` | 401 | No | Increment failed attempt counter |
| Account locked | `ACCOUNT_LOCKED` | 403 | No | Return `unlocksAt` timestamp |
| Invalid JWT | — | 401 | No | Return null from `verifyToken` |
| DB unavailable | `INTERNAL_ERROR` | 500 | Yes | Do not expose DB error details |
| Validation failure | `VALIDATION_ERROR` | 400 | No | Return field-level messages |

---

## 7. Dependencies

| Dependency | Why needed | Import path |
|------------|-----------|-------------|
| `bcrypt` | Password hash verification | `import bcrypt from 'bcryptjs'` |
| `jsonwebtoken` | JWT sign and verify | `import jwt from 'jsonwebtoken'` |
| `db` | User lookup and attempt tracking | `import { db } from '@/server/db'` |
| `logger` | Error logging on 500s | `import { logger } from '@/lib/logger'` |
| `env` | JWT secret, lockout config | `import { env } from '@/lib/env'` |

---

## 8. Configuration Constants

```typescript
const LOCKOUT_THRESHOLD = 5          // failed attempts before lock
const LOCKOUT_DURATION_MS = 15 * 60 * 1000  // 15 minutes
const JWT_EXPIRY = '7d'
const BCRYPT_ROUNDS = 12
```

---

## 9. File / Module Layout

```
src/server/auth/
├── index.ts          # Public exports — only what consumers need
├── login.ts          # login() function
├── token.ts          # signToken(), verifyToken()
├── lockout.ts        # recordFailedAttempt(), clearFailedAttempts(), getAccountLockStatus()
└── types.ts          # LoginInput, AuthResult, AuthError — all types for this module
```

---

## 10. Test Cases (Unit Level)

> These feed directly into the test file for this module.

- `login()` with valid credentials → returns token + user object
- `login()` with unknown email → throws `INVALID_CREDENTIALS` (not `NOT_FOUND`)
- `login()` with wrong password → throws `INVALID_CREDENTIALS` + increments counter
- `login()` on locked account → throws `ACCOUNT_LOCKED` with `unlocksAt`
- `recordFailedAttempt()` called 5 times → account becomes locked
- `clearFailedAttempts()` after success → counter resets to 0
- `verifyToken()` with valid token → returns payload
- `verifyToken()` with expired token → returns null
- `verifyToken()` with tampered signature → returns null
