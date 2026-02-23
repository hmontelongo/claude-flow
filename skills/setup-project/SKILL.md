---
name: setup-project
description: Set up project-specific Claude Code configuration for a new project. Asks questions, then appends rules to the Boost-generated CLAUDE.md. Use once at project start.
disable-model-invocation: true
---

You are helping set up Claude Code for a new Laravel project. Boost has already generated the foundational CLAUDE.md with Laravel conventions, Livewire, Flux UI, Pest, Tailwind, and Pint rules. You need to generate the PROJECT-SPECIFIC additions.

**Ask the user these questions, one group at a time:**

## Group 1: Project Identity
1. What does this project do? (one sentence)
2. Who are the primary users?
3. What language should user-facing copy be in?
4. What currency format does this project use?

## Group 2: Architecture
5. Does this project have multiple subdomains or portals? If yes, list them (e.g., admin.app.test, portal.app.test)
6. Will you use Horizon for queues? If yes, what queue names do you plan to use?
7. Are there any external APIs or services this project will integrate with?

## Group 3: Design System
8. What's the personality of this app? (corporate/serious, friendly/casual, or minimal/clean)
9. What color should represent the primary action? (Flux default is indigo — change or keep?)
10. Preferred density: compact (tight spacing, dense info) or comfortable (generous whitespace)?

## Group 4: Workflow Preferences
11. Any specific git rules? (Default: NEVER run git commands without explicit permission)
12. Do you want the mandatory visual verification rule? (Default: yes -- CC must open and see every UI change in the browser before calling it done)

---

**After getting answers, generate a block of markdown to APPEND to the existing CLAUDE.md.** Format:

```markdown
## Project: [Name]

[One sentence description]

### Users & Locale
- **Primary users**: [description]
- **Language**: [language]
- **Currency**: [format]

### Subdomains
- [list if applicable]

### Queue Structure
[If Horizon, list queues. If not, skip this section]

### External Integrations
[List APIs/services if any, with pointers to cached docs in .ai/docs/]

### Design System
- **Personality**: [corporate/friendly/minimal]
- **Primary action color**: [color]
- **Density**: [compact/comfortable]
- **Color semantics**: primary = [action], success/active = [color], warning/pending = [color], danger/error = red, neutral = zinc
- **Spacing rhythm**: sections gap-[N], groups gap-[N], items gap-[N], card padding p-[N]
- **Foundational components to create**: page-header, section-group, stat-card, status-badge, empty-state, price-display (if currency app), info-row

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
