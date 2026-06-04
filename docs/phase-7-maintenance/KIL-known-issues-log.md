# Known Issues & Decisions Log
> `KIL` · Phase 7 — Maintenance · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Paste at the start of any maintenance or refactor agent session. Critical for preventing agents from "fixing" things that were deliberately left alone.
>
> **Format:** `[ ]` = active / unresolved · `[x]` = resolved (keep for history)

---

## Intentional Shortcuts

> Code that was written a known-worse way on purpose, to ship faster. Agents must NOT "improve" these without explicit instruction.

- [ ] **[YYYY-MM-DD] [Short title]**
  - **What:** [Describe the shortcut — e.g., "Auth tokens are stored in localStorage instead of httpOnly cookies"]
  - **Why left:** [e.g., "httpOnly cookie approach requires additional server-side session management; deferred until user growth justifies it"]
  - **Workaround:** [e.g., "XSS risk mitigated by strict CSP headers"]
  - **Fix when:** [e.g., "When MAU > 500 or a security audit is scheduled"]

- [ ] **[YYYY-MM-DD] [Short title]**
  - **What:** [Description]
  - **Why left:** [Rationale]
  - **Workaround:** [Current mitigation, if any]
  - **Fix when:** [Trigger condition]

---

## Known Bugs

> Bugs that are known, accepted, and not being fixed now. Agents must NOT attempt to fix these silently.

- [ ] **[YYYY-MM-DD] [Bug title]**
  - **Symptoms:** [What the user sees — e.g., "Pagination resets to page 1 after filtering"]
  - **Root cause:** [e.g., "Filter state and page state are not coordinated in the URL"]
  - **Impact:** Low — workaround is to re-navigate
  - **Fix when:** [e.g., "Next refactor of the list page component"]

- [ ] **[YYYY-MM-DD] [Bug title]**
  - **Symptoms:** [Description]
  - **Root cause:** [Description]
  - **Impact:** [Low / Medium / High]
  - **Fix when:** [Trigger]

---

## Deferred Features

> Features that were explicitly scoped out of v1 and will be built later. Do not implement these without a deliberate decision.

- [ ] **[YYYY-MM-DD] [Feature name]**
  - **Description:** [What this feature would do]
  - **Why deferred:** [e.g., "Requires multi-tenant DB architecture that's out of scope for v1"]
  - **Depends on:** [Prerequisites before this can be built — e.g., "ADR-05 decision on multi-tenancy approach"]

- [ ] **[YYYY-MM-DD] [Feature name]**
  - **Description:** [Description]
  - **Why deferred:** [Reason]
  - **Depends on:** [Dependencies]

---

## Resolved (Keep for History)

- [x] **[YYYY-MM-DD] [Title]** — Resolved [YYYY-MM-DD]
  - **Was:** [What the issue was]
  - **Fix:** [How it was resolved]

---

<!--
## Template — copy for each new entry

### Category: [Shortcuts / Bugs / Deferred]

- [ ] **[YYYY-MM-DD] [Title]**
  - **What/Symptoms:** [...]
  - **Why left/Root cause:** [...]
  - **Workaround:** [...]
  - **Fix when:** [...]

-->
