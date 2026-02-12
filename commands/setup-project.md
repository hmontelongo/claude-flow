---
description: Set up project-specific Claude Code configuration for a new project. Asks questions, then appends rules to the Boost-generated CLAUDE.md.
---

You are helping set up Claude Code for a new Laravel project. Boost has already generated the foundational CLAUDE.md with Laravel conventions, Livewire, Flux UI, Pest, Tailwind, and Pint rules. You need to generate the PROJECT-SPECIFIC additions.

**Ask the user these questions, one group at a time:**

## Group 1: Project Identity
1. What does this project do? (one sentence)
2. Who are the primary users? (e.g., real estate agents, restaurant owners, internal team)
3. What language should user-facing copy be in? (e.g., Spanish for Mexican market)

## Group 2: Architecture
4. Does this project have multiple subdomains or portals? If yes, list them (e.g., admin.app.test, agents.app.test)
5. Will you use Horizon for queues? If yes, what queue names do you plan to use?
6. Are there any external APIs or services this project will integrate with?

## Group 3: Workflow Preferences
7. Any specific git rules? (Default: NEVER run git commands without explicit permission)
8. Do you want the mandatory visual verification rule? (Default: yes — CC must open and see every UI change in the browser before calling it done)

---

**After getting answers, generate a block of markdown to APPEND to the existing CLAUDE.md.** Format:

```markdown
## Project: [Name]

[One sentence description]

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

### ⛔ GIT COMMANDS FORBIDDEN ⛔
NEVER run ANY git commands without EXPLICIT user permission. This includes checkout, reset, clean, stash, revert, merge, rebase, push, pull. ALWAYS ask first.

### Code Quality
- After features: use the `code-reviewer` agent, then `vendor/bin/pint --dirty`
- Before UI work: use the `ux-reviewer` agent to plan the approach
- During UI work: use `verify-ui` to see what you built

### DO NOT
- Run git commands without explicit permission
- Install new packages without asking
- Use raw HTML when Flux UI has a component
- Use Alpine.js before exhausting Livewire options
- Put PHP logic in Blade views — use computed properties
- Build UI without visually verifying it in the browser
- Write mocked tests when asked to "test a flow" — use Pest 4 browser tests
```

**Then ask the user to confirm before appending it to CLAUDE.md.**

Also remind the user to:
1. Update `.claude/skills/test-app/SKILL.md` with project URLs and test credentials
2. Run `/design-system` before building any pages
