---
name: setup-project
description: Set up project-specific Claude Code configuration for a new project. Asks questions, then appends rules to the Boost-generated CLAUDE.md. Use once at project start.
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

## Group 3: Workflow Preferences
8. Any specific git rules? (Default: NEVER run git commands without explicit permission)
9. Do you want the mandatory visual verification rule? (Default: yes — CC must open and see every UI change in the browser before calling it done)

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

### DO NOT
- Run git commands without explicit permission
- Install new packages without asking
- Build UI without visually verifying it in the browser
```

Note: Rules about Flux UI, Alpine.js, Blade views, and testing conventions are enforced by the always-loaded guidelines in `.ai/guidelines/`. Do NOT duplicate them in CLAUDE.md.

**Then ask the user to confirm before appending it to CLAUDE.md.**

Also remind the user to run `/design-system` before building any pages.
