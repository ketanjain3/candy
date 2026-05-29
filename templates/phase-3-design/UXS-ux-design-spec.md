# UI/UX Design Specification
> `UXS` · Phase 3 — Design · Last updated: [YYYY-MM-DD]
>
> **Agent use:** Context for frontend/UI agent sessions — attach when generating components or layouts.

---

## 1. Design Principles

- [Principle 1 — e.g., "Clarity over cleverness — obvious > elegant"]
- [Principle 2 — e.g., "Mobile-first: all layouts work at 375px before scaling up"]
- [Principle 3 — e.g., "Progressive disclosure: show only what's needed for the current task"]

---

## 2. Design System

- **Component library:** [e.g., shadcn/ui built on Radix primitives]
- **Styling:** [e.g., Tailwind CSS — utility classes only, no custom CSS unless unavoidable]
- **Icon set:** [e.g., Lucide React]
- **Typography:** [e.g., Inter via next/font]
- **Color palette:**
  - Background: `[e.g., zinc-950]`
  - Surface: `[e.g., zinc-900]`
  - Border: `[e.g., zinc-800]`
  - Primary action: `[e.g., indigo-500]`
  - Destructive: `[e.g., red-500]`
  - Text primary: `[e.g., zinc-50]`
  - Text muted: `[e.g., zinc-400]`

---

## 3. User Flows

### Flow 1 — [e.g., Sign Up → First Action]

```
Landing Page
    └── Click "Get Started"
            └── Sign Up Form
                    └── [success] Email Verification Prompt
                    │       └── Verify Link Clicked
                    │               └── Onboarding / Dashboard
                    └── [error] Inline validation message
```

### Flow 2 — [e.g., Core Task Flow]

```
Dashboard
    └── Click "[Action]"
            └── [Modal / New Page]
                    └── Fill form
                            └── [success] Confirmation + updated list
                            └── [error] Error toast + form preserved
```

---

## 4. Screen Descriptions

### Screen: Landing Page

- **Purpose:** Convert visitors to sign-ups
- **Layout:** Centered single-column, max-width 640px
- **Sections:**
  - Hero headline + subheadline + primary CTA button
  - 3 feature bullets
  - Secondary CTA (login link)
- **Key states:** Default only (no auth-aware variation in v1)

```
┌─────────────────────────┐
│       [Logo]            │
│                         │
│   Big Headline Here     │
│   Subheadline text      │
│                         │
│   [ Get Started →  ]    │
│                         │
│  • Feature 1            │
│  • Feature 2            │
│  • Feature 3            │
│                         │
│  Already have account?  │
│  Log in                 │
└─────────────────────────┘
```

---

### Screen: Dashboard

- **Purpose:** Home base — shows user's [entities] and primary actions
- **Layout:** Sidebar nav (240px) + main content area
- **Sections:**
  - Sidebar: nav links, user avatar + logout
  - Main: page title + "[Create]" button + list/grid of items
- **Key states:**
  - Empty state: illustration + "Create your first [thing]" CTA
  - Loading state: skeleton cards
  - Populated: list of item cards

```
┌──────────┬──────────────────────────────┐
│ [Logo]   │  [Items]        [+ Create]   │
│          │ ─────────────────────────── │
│ Dashboard│  ┌──────────┐ ┌──────────┐  │
│ [Entity] │  │  Item 1  │ │  Item 2  │  │
│ Settings │  │  ...     │ │  ...     │  │
│          │  └──────────┘ └──────────┘  │
│          │                             │
│ [Avatar] │                             │
└──────────┴──────────────────────────────┘
```

---

### Screen: [Entity] Detail

- **Purpose:** View and edit a single [entity]
- **Layout:** Single column, max-width 768px, centered
- **Sections:** [Field list / form / action buttons]
- **Key states:** View mode / Edit mode / Saving / Error

---

### Screen: Settings

- **Purpose:** Account management
- **Layout:** Two-column on desktop (nav left, content right); stacked on mobile
- **Sections:** Profile, Password, Notifications, Danger zone (delete account)

---

## 5. Component Inventory

| Component | States | Notes |
|-----------|--------|-------|
| `Button` | default, hover, disabled, loading | Use `variant="destructive"` for delete actions |
| `Input` | default, focus, error, disabled | Always pair with a `Label` |
| `Card` | default, hover (if clickable) | Use for list items |
| `Modal / Dialog` | open, closed, loading | Confirm destructive actions before executing |
| `Toast` | success, error, info | Auto-dismiss after 4 seconds |
| `Skeleton` | — | Use during data loading, not a spinner |
| `EmptyState` | — | Every list view needs one |
| `Avatar` | with image, fallback initials | |

---

## 6. Interaction Rules

- **Optimistic updates:** Apply UI changes immediately, revert on error + show toast
- **Form validation:** Inline, on-blur — not on-submit-only
- **Loading states:** Button shows spinner; disable during in-flight request
- **Destructive actions:** Always require a confirmation dialog before executing
- **Error messages:** Human-readable; never expose technical details to the user
- **Empty states:** Every list or table must have one — no blank pages

---

## 7. Accessibility Requirements

- All interactive elements keyboard-navigable (Tab, Enter, Space, Escape)
- Focus ring always visible (do not suppress `outline` globally)
- Color is never the sole means of conveying information
- All images have `alt` text; decorative images use `alt=""`
- Form inputs have associated `<label>` elements
- ARIA roles and labels on custom interactive components (modals, dropdowns)
- Minimum touch target: 44×44px
