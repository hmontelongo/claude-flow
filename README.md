# claude-code-config

Shared Claude Code configuration for Laravel + Livewire + Flux UI projects.
Goes **on top of** what Laravel Boost already generates.

---

## How Everything Activates (The Honest Map)

Claude Code has 4 types of configuration, and they activate differently. Understanding this is essential — otherwise you're just hoping files get used.

### Guidelines → Always On (Automatic)

Files in `.ai/guidelines/` are loaded by Boost into CC's context on every session. CC reads them like it reads CLAUDE.md. You never invoke them — they're just *there*.

| File | What it enforces |
|------|-----------------|
| `guidelines/flux-ui/components.md` | Use Flux components first, extract Blade components, no raw HTML |
| `guidelines/flux-ui/anti-patterns.md` | 10 explicit things CC must NEVER do (raw buttons, custom modals, Alpine for forms, etc.) |

**Reliability: High.** Boost loads these automatically. CC sees them every session.

### Commands → You Type Them (Explicit)

You type `/command-name` in Claude Code. Deterministic, reliable, you control when they fire.

| Command | When you use it | What it does |
|---------|----------------|--------------|
| `/setup-project` | Once, at project start | Asks you questions, appends project-specific rules to CLAUDE.md |
| `/code-review` | After completing a feature | Calls the `code-reviewer` agent, then runs `pint --dirty` |
| `/ux-review` | After building/changing any UI | Calls the `ux-reviewer` agent to evaluate workflow, hierarchy, patterns |
| `/design-system` | Once, before building any UI | Defines colors, spacing, creates foundational Blade components |
| `/test-app` | When you want regression tests | CC writes Pest 4 browser tests for the specified flow |
| `/verify-ui` | During UI development | CC opens browser, screenshots, evaluates, iterates |
| `/fetch-docs` | When integrating external APIs | CC reads official docs instead of Googling |

**Reliability: High.** You type it, it runs.

**Note:** Skills and commands both create slash commands. A skill at `skills/verify-ui/SKILL.md` becomes `/verify-ui`. A command at `commands/code-review.md` becomes `/code-review`. The difference: skills can *also* auto-invoke (see below), and skills can bundle supporting files in their folder.

### Agents → Called By Commands (Reliable) or Auto-Invoked (Less Reliable)

Agents are specialized CC instances with their own context window. They're called by commands or by CC's judgment.

| Agent | Reliable trigger | Auto-invoke trigger |
|-------|-----------------|-------------------|
| `code-reviewer` | `/code-review` command explicitly calls it | CC *may* auto-invoke when it sees code review context |
| `ux-reviewer` | `/ux-review` command explicitly calls it | CC *may* auto-invoke when building UI — but don't count on it |

**Reliability: High when called by commands. Medium when relying on auto-invocation.**

### Skills → Slash Command (Reliable) or Auto-Invoke (Unreliable)

Skills have descriptions that *should* tell CC when to auto-invoke. Community feedback says: **auto-invocation is inconsistent**. So we treat it as a bonus, not a guarantee.

| Skill | Reliable trigger | What CC *should* auto-invoke for |
|-------|-----------------|--------------------------------|
| `verify-ui` | `/verify-ui` or CLAUDE.md rule (see below) | After writing any UI code |
| `design-system` | `/design-system` | When starting UI work or noticing inconsistencies |
| `ui-patterns` | `/ui-patterns` | Before building any new page/component |
| `test-app` | `/test-app` | When asked to write tests |
| `fetch-docs` | `/fetch-docs` | When integrating external APIs |

**The verify-ui problem:** This is the most critical skill — CC must see what it builds. We can't rely on auto-invocation. That's why `/setup-project` appends this hard rule to CLAUDE.md:

```
### Visual Verification (MANDATORY)
After implementing or modifying ANY user-facing code, you MUST:
1. Open the page in the browser using Playwright CLI
2. Take a screenshot and evaluate the result
3. Fix any issues found
4. Verify again until the implementation is solid
You do NOT tell the user "I've implemented X" without having seen it running.
Use the verify-ui skill for the full verification checklist.
```

This CLAUDE.md rule is the enforcement mechanism. The skill has the detailed instructions. Both are needed.

---

## The Development Workflow

Here's when each piece fires in a typical feature build:

```
1. START SESSION
   └── CC automatically loads: CLAUDE.md + Boost guidelines + Flux guidelines
       (guidelines/flux-ui/components.md and anti-patterns.md are active)

2. PLAN THE UI
   └── You type: /ux-review (or /ui-patterns to review available patterns)
       └── ux-reviewer agent plans the approach before any code is written

3. BUILD
   └── CC writes code following Flux guidelines (auto-loaded)
   └── After each visual change, CC must verify (CLAUDE.md rule):
       └── Uses verify-ui skill → opens browser, screenshots, evaluates, iterates

4. TEST
   └── You type: /test-app
       └── CC writes Pest 4 regression tests for the flow

5. REVIEW
   └── You type: /code-review
       └── code-reviewer agent analyzes code, then pint formats it
   └── You type: /ux-review
       └── ux-reviewer agent evaluates the final UX quality
```

---

## Repo Structure

```
claude-code-config/
├── README.md                               ← This file
├── agents/
│   ├── code-reviewer.md                    ← Subagent for code quality review
│   └── ux-reviewer.md                      ← Subagent for UX/workflow review
├── commands/
│   ├── setup-project.md                    ← /setup-project (one-time project config)
│   ├── code-review.md                      ← /code-review (calls agent + pint)
│   └── ux-review.md                        ← /ux-review (calls agent, opens browser)
├── skills/
│   ├── verify-ui/SKILL.md                  ← /verify-ui (MANDATORY build-verify loop)
│   ├── test-app/SKILL.md                   ← /test-app (Pest 4 regression tests)
│   ├── ui-patterns/SKILL.md                ← /ui-patterns (pattern library reference)
│   ├── design-system/SKILL.md              ← /design-system (establish visual language)
│   └── fetch-docs/SKILL.md                 ← /fetch-docs (cache external API docs)
└── guidelines/
    └── flux-ui/
        ├── components.md                   ← Always loaded: component-first rules
        └── anti-patterns.md                ← Always loaded: 10 explicit prohibitions
```

**Why SKILL.md?** Claude Code requires skills to be named `SKILL.md` — the folder name IS the skill name (`verify-ui/SKILL.md` → `/verify-ui`).

---

## New Project Setup

### One-Time Prerequisites

```bash
# Playwright CLI (for visual verification)
npm install -g @playwright/cli@latest
playwright-cli install-browser

# Context7 MCP (global, for non-Laravel package docs only — Boost handles Laravel docs)
claude mcp add -s user context7 npx -y @upstash/context7-mcp@latest
```

### Per Project (6 commands, ~15 minutes)

```bash
# 1. Create project (choose Livewire starter, Pest, your DB)
laravel new project-name && cd project-name

# 2. Install Boost (generates CLAUDE.md, guidelines, MCP servers)
composer require laravel/boost --dev && php artisan boost:install

# 3. Copy this repo into the project
cp -r ~/dev/claude-code-config/agents/ .claude/agents/
cp -r ~/dev/claude-code-config/skills/ .claude/skills/
cp -r ~/dev/claude-code-config/commands/ .claude/commands/
cp -r ~/dev/claude-code-config/guidelines/ .ai/guidelines/

# 4. Open Claude Code
claude

# 5. Configure project specifics (appends rules to CLAUDE.md including verify-ui mandate)
> /setup-project

# 6. Establish design system before any UI work
> /design-system
```

### What Boost Already Handles (Don't Duplicate)

`laravel new` + `boost:install` gives you: CLAUDE.md with foundational rules, AI guidelines for all detected packages (version-specific), Boost MCP (15+ tools), Herd MCP. This repo only adds what Boost doesn't generate.

### Per-Project Customization

| File | What to Change |
|------|----------------|
| `skills/test-app/SKILL.md` | URLs, test credentials, app structure |
| `agents/code-reviewer.md` | Domain-specific concerns (optional) |
| `agents/ux-reviewer.md` | User persona if different from default |

---

## How the Layers Stack

```
laravel new
└── Livewire + Flux UI + Fortify + Tailwind + Pest

boost:install
├── CLAUDE.md (foundational rules — always loaded)
├── .ai/guidelines/ (ecosystem guidelines — always loaded)
├── Boost MCP (15+ tools)
└── Herd MCP

THIS REPO
├── .ai/guidelines/flux-ui/ → always loaded (Boost picks these up)
├── .claude/commands/ → you type /command-name
├── .claude/agents/ → called by commands
└── .claude/skills/ → you type /skill-name (auto-invoke is unreliable)

/setup-project appends to CLAUDE.md
└── verify-ui mandate (always loaded, enforced every session)
└── git safety rules
└── project-specific context
```

## Maintenance

**Shared files**: Edit here → push to GitHub → `cp` into active projects.
**Per project**: New patterns → update ui-patterns skill here. New Blade components → update components.md here. Boost updates via `php artisan boost:update`.
