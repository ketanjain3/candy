# API Design Document
> `ADD` · Phase 3 — Design · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Attach to any session that involves building or consuming API endpoints. Treat as source of truth for request/response contracts.

---

## 1. Overview

- **Base URL (local):** `http://localhost:3000/api`
- **Base URL (prod):** `https://[your-domain]/api`
- **API style:** [REST / tRPC / GraphQL]
- **Versioning:** [e.g., URL prefix `/api/v1/` — bump major version only on breaking changes]
- **Auth:** [e.g., Bearer token (JWT) in `Authorization` header / session cookie]

---

## 2. Authentication

All protected endpoints require:

```
Authorization: Bearer <jwt_token>
```

Unauthenticated requests return `401 Unauthorized`.

Auth endpoints (below) are public.

---

## 3. Error Format

All errors return a consistent JSON body:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable description",
    "details": {}
  }
}
```

| HTTP Status | Code | Meaning |
|-------------|------|---------|
| 400 | `VALIDATION_ERROR` | Request body / params failed validation |
| 401 | `UNAUTHORIZED` | Missing or invalid auth token |
| 403 | `FORBIDDEN` | Authenticated but not permitted |
| 404 | `NOT_FOUND` | Resource does not exist |
| 409 | `CONFLICT` | State conflict (e.g., duplicate email) |
| 422 | `UNPROCESSABLE` | Request valid but business rule violated |
| 500 | `INTERNAL_ERROR` | Server-side failure |

---

## 4. Endpoint Catalog

### Auth

#### `POST /api/auth/register`
Create a new user account.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "min8chars"
}
```

**Response `201`:**
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "created_at": "2024-06-01T00:00:00Z"
  }
}
```

---

#### `POST /api/auth/login`
Authenticate and receive a session token.

**Request:**
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Response `200`:**
```json
{
  "token": "jwt_token_string",
  "user": {
    "id": "uuid",
    "email": "user@example.com"
  }
}
```

---

#### `POST /api/auth/logout`
Invalidate the current session. Auth required.

**Response `204`:** No body.

---

### [Resource: e.g., Items]

#### `GET /api/items`
List all items for the authenticated user.

**Query params:**
| Param | Type | Required | Description |
|-------|------|----------|-------------|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Items per page (default: 20, max: 100) |
| `status` | string | No | Filter by status: `active`, `archived` |

**Response `200`:**
```json
{
  "data": [
    {
      "id": "uuid",
      "title": "Item title",
      "status": "active",
      "created_at": "2024-06-01T00:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 42
  }
}
```

---

#### `POST /api/items`
Create a new item.

**Request:**
```json
{
  "title": "Item title",
  "[field]": "[value]"
}
```

**Response `201`:**
```json
{
  "data": {
    "id": "uuid",
    "title": "Item title",
    "status": "active",
    "created_at": "2024-06-01T00:00:00Z"
  }
}
```

---

#### `GET /api/items/:id`
Get a single item by ID.

**Response `200`:** Single item object.
**Response `404`:** Item not found or not owned by user.

---

#### `PATCH /api/items/:id`
Update an item. Partial updates supported.

**Request:**
```json
{
  "title": "Updated title"
}
```

**Response `200`:** Updated item object.

---

#### `DELETE /api/items/:id`
Delete an item.

**Response `204`:** No body.

---

### Webhooks

#### `POST /api/webhooks/[service]`
Receive webhook events from [e.g., Stripe].

- Validates signature using `[SERVICE]_WEBHOOK_SECRET`
- Returns `200` immediately; processing is async
- Events: [list event types handled — e.g., `checkout.session.completed`, `customer.subscription.deleted`]

---

## 5. Rate Limiting

| Tier | Limit |
|------|-------|
| Unauthenticated | [e.g., 20 req/min per IP] |
| Authenticated | [e.g., 100 req/min per user] |
| Auth endpoints | [e.g., 10 req/min per IP — brute force protection] |
