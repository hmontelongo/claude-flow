---
name: setup-project
description: Set up project-specific Claude Code configuration for a new project. Asks questions, then appends rules to the Boost-generated CLAUDE.md. Use once at project start.
disable-model-invocation: true
---

You are helping set up Claude Code for a new Laravel project. Boost has already generated the foundational CLAUDE.md with Laravel conventions, Livewire, Flux UI, Pest, Tailwind, and Pint rules. You need to generate the PROJECT-SPECIFIC additions.

**Ask the user these questions, one group at a time:**

## Group 1: Project Context
1. What does this project do? (one sentence)
2. Who are the primary users?
3. Locale: what language and region? (e.g., "Spanish — Spain", "English — US") Mention currency if the project handles money.

## Group 2: Design System
4. What's the personality of this app? (corporate/serious, friendly/casual, or minimal/clean)
5. What color should represent the primary action? (Flux default is indigo — change or keep?)
6. Preferred density: compact (tight spacing, dense info) or comfortable (generous whitespace)?
7. Which page types will this project have? (checklist: list pages, detail pages, form pages, dashboard)

## Group 3: Workflow Preferences
8. Any specific git rules? (Default: NEVER run git commands without explicit permission)
9. Do you want the mandatory visual verification rule? (Default: yes -- CC must open and see every UI change in the browser before calling it done)

---

**After getting answers, generate a block of markdown to APPEND to the existing CLAUDE.md.** Format:

```markdown
## Project: [Name]

[One sentence description]

### Context
- **Primary users**: [description]
- **Locale**: [language — region, plus currency format if applicable]

### Design System

See `guidelines/frontend/design-system.md` for component contracts and archetype details.

**Personality**: [corporate/friendly/minimal]

**Token Semantics**:
| Token | Color | Intent |
|-------|-------|--------|
| Primary action | [color] | Buttons, active states, links, focus rings |
| Success / active | [color] | Confirmations, active statuses, positive indicators |
| Warning / pending | [color] | Alerts, pending statuses, attention-needed states |
| Danger / error | red | Destructive actions, error states, validation failures |
| Neutral | zinc | Borders, muted text, secondary surfaces, disabled states |
| Accent (optional) | [color or "none"] | Highlights, decorative elements, brand differentiation |

**Spacing Rhythm** ([compact/comfortable]):
| Level | Value |
|-------|-------|
| Page sections | gap-[N] |
| Groups | gap-[N] |
| Items | gap-[N] |
| Card internals | p-[N] |

**Foundational Components** (based on page types from Q7):
| Component | Purpose |
|-----------|---------|
| page-header | Page title, subtitle, action buttons |
| section-group | Groups related content with heading |
| empty-state | Placeholder for empty collections |
| status-badge | Semantic status with color mapping |
| [Include if detail pages] info-row | Label-value pairs for detail pages |
| [Include if dashboard] stat-card | Single metric display for dashboards |

Create each component when you first need it. Add project-specific components as they emerge.

**Page Archetypes in Use**:
- [List from Q7, e.g.: list pages, detail pages, form pages, dashboard]

### API Verification (IMPORTANT)
Always use `search-docs` to verify Livewire and Flux component APIs before implementing. Never assume props, methods, or component names -- check the installed version.

### Visual Verification (MANDATORY)
After implementing or modifying ANY user-facing code, you MUST:
1. Open the page in the browser using Playwright CLI
2. Take a screenshot and evaluate the result
3. Fix any issues found
4. Verify again until the implementation is solid
You do NOT tell the user "I've implemented X" without having seen it running.
Use the `verify-ui` skill for the full verification checklist.

### Git Safety
NEVER run ANY git commands without EXPLICIT user permission. This includes checkout, reset, clean, stash, revert, merge, rebase, push, pull. ALWAYS ask first.

### Compaction Preservation
When context is compacted, always preserve: list of modified files, current task state, active decisions, errors being debugged, and all project context from this CLAUDE.md.

### DO NOT
- Run git commands without explicit permission
- Install new packages without asking
- Build UI without visually verifying it in the browser
- Assume component APIs without checking search-docs
```

Note: Rules about Flux UI, Alpine.js, Blade views, and testing conventions are enforced by the always-loaded guidelines in `.ai/guidelines/`. Do NOT duplicate them in CLAUDE.md.

**Then ask the user to confirm before appending it to CLAUDE.md.**

Also remind the user to establish their design system's foundational Blade components before building any pages.
