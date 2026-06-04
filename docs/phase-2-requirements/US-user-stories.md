# User Stories / Product Backlog
> `US` · Phase 2 — Requirements · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Per-session task context — feed the relevant story + acceptance criteria as the work unit for a coding agent session.

---

## Epic: [Epic Name — e.g., Authentication]

> [One sentence describing what this epic covers and why it matters]

- [ ] **US-01 · Must** — As a [visitor], I want to [sign up with my email and password], so that [I have a personal account to save my work].
  - Given I submit a valid email and password → account is created and I receive a verification email
  - Given I submit an already-registered email → I see an error and am prompted to log in
  - Given I submit a password shorter than 8 characters → I see an inline validation error before submitting

- [ ] **US-02 · Must** — As a [registered user], I want to [log in with my email and password], so that [I can access my account].
  - Given correct credentials → I am redirected to the dashboard
  - Given incorrect password → I see an error; no lockout until 5 failed attempts
  - Given 5 failed attempts → account is temporarily locked for 15 minutes

- [ ] **US-03 · Must** — As a [registered user], I want to [reset my password via email], so that [I can regain access if I forget it].
  - Given I submit my email → a reset link is sent within 60 seconds
  - Given I click the link within 1 hour → I can set a new password
  - Given the link is older than 1 hour → I see an "expired link" error and can request a new one

---

## Epic: [Epic Name — e.g., Core Feature]

> [One sentence description]

- [ ] **US-10 · Must** — As a [user persona], I want to [action], so that [outcome].
  - [Acceptance criterion — Given / When / Then or plain English]
  - [Acceptance criterion]
  - [Edge case]

- [ ] **US-11 · Should** — As a [user persona], I want to [action], so that [outcome].
  - [Acceptance criterion]
  - [Acceptance criterion]

- [ ] **US-12 · Could** — As a [user persona], I want to [action], so that [outcome].
  - [Acceptance criterion]

---

## Epic: [Epic Name — e.g., Settings & Account]

> [One sentence description]

- [ ] **US-20 · Must** — As a [user], I want to [update my email address], so that [I can keep my account current].
  - [Acceptance criterion]
  - [Acceptance criterion]

- [ ] **US-21 · Should** — As a [user], I want to [delete my account], so that [I can remove all my data].
  - [Acceptance criterion — e.g., "Deletion is soft-deleted for 30 days before permanent removal"]
  - [Acceptance criterion]

---

## Backlog (Unscheduled)

> Stories parked here are not planned for v1. Move up when prioritized.

- [ ] **US-50 · Could** — As a [user persona], I want to [feature], so that [outcome].
- [ ] **US-51 · Could** — As a [user persona], I want to [feature], so that [outcome].
