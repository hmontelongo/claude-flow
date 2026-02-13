# claude-code-config

Shared Claude Code configuration for Laravel + Livewire + Flux UI projects.
Goes **on top of** what Laravel Boost already generates.

---

## How Everything Activates

Claude Code has 3 types of configuration in this repo, and they activate differently.

### Guidelines → Always On (Automatic)

Files in `.ai/guidelines/` are loaded by Boost into CC's context every session. You never invoke them — they're just *there*.

| File | What it enforces |
|------|-----------------|
| `guidelines/flux-ui/components.md` | Flux-first hierarchy, component extraction, no raw HTML, no Alpine abuse |
| `guidelines/flux-ui/anti-patterns.md` | 11 explicit things CC must NEVER do |
| `guidelines/laravel/conventions.md` | Architecture layers, Eloquent, return types, Form Requests, testing |
| `guidelines/livewire/conventions.md` | Single-file components, state, wire:model, computed properties, performance |

**Reliability: High.** Boost loads these automatically. CC sees them every session.

### Skills → You Type Them (Explicit) or Auto-Invoke (Unreliable)

You type `/skill-name` in Claude Code. Skills can also auto-invoke based on their description, but **auto-invocation is inconsistent** — treat it as a bonus, not a guarantee.

| Skill | When you use it | What it does |
|-------|----------------|--------------|
| `/setup-project` | Once, at project start | Asks questions, appends project-specific rules to CLAUDE.md |
| `/code-review` | After completing a feature | Calls the `code-reviewer` agent, then runs `pint --dirty` |
| `/ux-review` | After building/changing any UI | Calls the `ux-reviewer` agent to evaluate workflow, hierarchy, patterns |
| `/design-system` | Once, before building any UI | Defines colors, spacing, creates foundational Blade components |
| `/test-app` | When you want regression tests | CC writes Pest 4 browser tests for the specified flow |
| `/verify-ui` | During UI development | CC opens browser, screenshots, evaluates, iterates |
| `/fetch-docs` | When integrating external APIs | CC reads official docs instead of Googling |
| `/ui-patterns` | Before building any new page | Reference patterns for lists, forms, dashboards, detail pages |

**Reliability: High when typed. Medium for auto-invocation.**

**The verify-ui problem:** CC must see what it builds. We can't rely on auto-invocation. That's why `/setup-project` appends this hard rule to CLAUDE.md:

```
### Visual Verification (MANDATORY)
After implementing or modifying ANY user-facing code, you MUST:
1. Open the page in the browser using Playwright CLI
2. Take a screenshot and evaluate the result
3. Fix any issues found
4. Verify again until the implementation is solid
```

### Agents → Called By Skills (Reliable)

Agents are specialized CC instances with their own context window. Called by skills.

| Agent | Triggered by | What it does |
|-------|-------------|-------------|
| `code-reviewer` | `/code-review` | Reviews code for Laravel craftsmanship and simplicity |
| `ux-reviewer` | `/ux-review` | Evaluates UX: workflow logic, hierarchy, consistency |

---

## The Development Workflow

```
1. START SESSION
   └── CC automatically loads: CLAUDE.md + Boost guidelines + all guidelines/

2. PLAN THE UI
   └── You type: /ux-review (or /ui-patterns to review available patterns)
       └── ux-reviewer agent plans the approach before any code is written

3. BUILD
   └── CC writes code following guidelines (auto-loaded)
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
├── settings.local.json                     ← Hooks config (copy to .claude/ per project)
├── agents/
│   ├── code-reviewer.md                    ← Subagent for code quality review
│   └── ux-reviewer.md                      ← Subagent for UX/workflow review
├── skills/
│   ├── setup-project/SKILL.md              ← /setup-project (one-time project config)
│   ├── code-review/SKILL.md                ← /code-review (calls agent + pint)
│   ├── ux-review/SKILL.md                  ← /ux-review (calls agent, opens browser)
│   ├── verify-ui/SKILL.md                  ← /verify-ui (MANDATORY build-verify loop)
│   ├── test-app/SKILL.md                   ← /test-app (Pest 4 regression tests)
│   ├── ui-patterns/SKILL.md                ← /ui-patterns (pattern library reference)
│   ├── design-system/SKILL.md              ← /design-system (establish visual language)
│   └── fetch-docs/SKILL.md                 ← /fetch-docs (cache external API docs)
└── guidelines/
    ├── flux-ui/
    │   ├── components.md                   ← Always loaded: Flux-first rules (~30 lines)
    │   └── anti-patterns.md                ← Always loaded: 11 explicit prohibitions
    ├── laravel/
    │   └── conventions.md                  ← Always loaded: architecture, Eloquent, testing
    └── livewire/
        └── conventions.md                  ← Always loaded: state, wire:model, performance
```

---

## New Project Setup

### One-Time Prerequisites

```bash
# Playwright CLI (for visual verification during development)
npm install -g @playwright/cli@latest
playwright-cli install-browser

# Context7 MCP (global, for non-Laravel package docs only — Boost handles Laravel docs)
claude mcp add -s user context7 npx -y @upstash/context7-mcp@latest
```

### Per Project

```bash
# 1. Create project (choose Livewire starter, Pest, your DB)
laravel new project-name && cd project-name

# 2. Install Boost (generates CLAUDE.md, guidelines, MCP servers)
composer require laravel/boost --dev && php artisan boost:install

# 3. Copy this repo into the project
cp -r ~/Code/claude-code-config/agents/ .claude/agents/
cp -r ~/Code/claude-code-config/skills/ .claude/skills/
cp -r ~/Code/claude-code-config/guidelines/ .ai/guidelines/

# 4. Copy hooks config (auto-formats PHP with Pint after every edit)
cp ~/Code/claude-code-config/settings.local.json .claude/settings.local.json

# 5. Open Claude Code
claude

# 6. Configure project specifics (appends rules to CLAUDE.md including verify-ui mandate)
> /setup-project

# 7. Establish design system before any UI work
> /design-system
```

### What Boost Already Handles (Don't Duplicate)

`laravel new` + `boost:install` gives you: CLAUDE.md with foundational rules, AI guidelines for all detected packages (version-specific), Boost MCP (15+ tools), Herd MCP. This repo only adds what Boost doesn't generate.

### Per-Project Customization

All project-specific context lives in CLAUDE.md (generated by `/setup-project`). Agents and skills read from CLAUDE.md — no manual editing of agent files needed.

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
├── .ai/guidelines/ → always loaded (Flux UI, Laravel, Livewire conventions)
├── .claude/skills/ → you type /skill-name
├── .claude/agents/ → called by skills
└── .claude/settings.local.json → hooks (auto-format with Pint)

/setup-project appends to CLAUDE.md
└── verify-ui mandate (always loaded, enforced every session)
└── git safety rules
└── project-specific context (domain, users, locale, currency)
```

## Hooks

The `settings.local.json` file configures a PostToolUse hook that auto-runs `vendor/bin/pint` on any PHP file CC writes or edits. Copy it to `.claude/settings.local.json` in each project. It's gitignored by default (`.claude/settings.local.json` is not committed).

## Maintenance

**Shared files**: Edit here → push to GitHub → `cp` into active projects.
**Per project**: New patterns → update ui-patterns skill here. Boost updates via `php artisan boost:update`.
