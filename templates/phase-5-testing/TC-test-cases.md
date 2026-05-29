# Test Cases / Acceptance Criteria
> `TC` · Phase 5 — Testing · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Feed acceptance criteria to a coding agent → agent generates automated tests (Jest, Vitest, Pytest, Playwright). Acceptance criteria here are the specification; passing tests are the living document.

---

## How to Use This Document

1. Pick a user story from `US-user-stories.md`
2. Copy its acceptance criteria into this file under the matching epic
3. Pass the story + criteria to a coding agent: _"Write tests for US-01 using the Given/When/Then criteria below"_
4. Once tests are written and passing, the story is done — the test file becomes the living record

---

## Critical Path Scenarios

> These must pass on every deploy. They are the minimum viable test suite.

- [ ] User can sign up, verify email, and log in
- [ ] User can complete the core action ([ describe the single most important user action ])
- [ ] User can log out
- [ ] Unauthenticated request to protected API returns 401
- [ ] [Add your critical path scenario]

---

## Epic: Authentication

### US-01 — Sign Up

**Given** a visitor submits a valid email and password (≥ 8 chars)
**When** the form is submitted
**Then** an account is created and a verification email is sent

**Given** a visitor submits an already-registered email
**When** the form is submitted
**Then** an error message is shown and no duplicate account is created

**Given** a visitor submits a password shorter than 8 characters
**When** the form is submitted (or on blur)
**Then** an inline validation error appears before the request is sent

---

### US-02 — Login

**Given** a user submits correct credentials
**When** login is submitted
**Then** a session is created and the user is redirected to the dashboard

**Given** a user submits an incorrect password fewer than 5 times
**When** login is submitted
**Then** an error message is shown; account remains accessible

**Given** a user fails login 5 times consecutively
**When** the 5th failure occurs
**Then** the account is temporarily locked for 15 minutes

---

### US-03 — Password Reset

**Given** a registered user submits their email
**When** the reset form is submitted
**Then** a reset link is sent within 60 seconds

**Given** a user clicks a reset link within 1 hour
**When** they submit a new password
**Then** the password is updated and they can log in with the new password

**Given** a user clicks a reset link older than 1 hour
**When** they arrive at the reset page
**Then** they see an "expired link" error and can request a new one

---

## Epic: [Core Feature]

### US-10 — [Story Title]

**Given** [precondition]
**When** [action]
**Then** [expected outcome]

**Given** [edge case precondition]
**When** [action]
**Then** [expected outcome]

---

### US-11 — [Story Title]

**Given** [precondition]
**When** [action]
**Then** [expected outcome]

---

## Edge Cases to Cover

> Scenarios not tied to a specific story but important for robustness.

- [ ] API rate limit is enforced — 101st request in 1 minute returns 429
- [ ] Empty list state renders correctly — no blank page
- [ ] Form preserves input on failed submission
- [ ] Concurrent updates to the same record are handled without data corruption
- [ ] [Add your edge case]

---

## Out of Scope for Automated Testing (v1)

- Performance / load testing — manual review of metrics post-deploy
- Visual regression — review screenshots manually on deploy
- [Other items intentionally not automated]
