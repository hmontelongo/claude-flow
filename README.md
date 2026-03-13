# claude-flow

Shared Claude Code configuration for Laravel + Livewire + Flux UI projects.
Goes **on top of** what Laravel Boost already generates.

---

## Features

- **Guidelines** (always loaded): Architecture conventions, frontend patterns, anti-patterns -- opinions only, no API specifics that go stale
- **Rules** (always loaded): Thinking framework, workflow checkpoints, Playwright reference, compaction strategy
- **Skills** (user-invoked): Code review, UX review, visual verification, test writing, project setup
- **Agents** (called by skills): Specialized subagents with tool restrictions and persistent project memory
- **Permissions + environment**: Pre-approved commands, denied destructive commands, lazy-loaded MCP tools, auto-updater disabled
- **Hooks**: Auto-format PHP with Pint, warn on uncommitted changes before edits

---

## How Everything Activates

### Guidelines (Always On)

Files in `.ai/guidelines/` are loaded by Boost into CC's context every session.

| File | What it enforces |
|------|-----------------|
| `guidelines/architecture/conventions.md` | Architecture layers, Eloquent principles, return types, Form Requests, testing philosophy |
| `guidelines/frontend/conventions.md` | Flux-first hierarchy, Livewire conventions, wire:model, page structure, designer boundary |
| `guidelines/frontend/design-system.md` | Design tokens, foundational components, page archetypes |
| `guidelines/quality/anti-patterns.md` | UX essentials and prohibitions not covered by conventions files |

### Rules (Always On)

Files in `.claude/rules/` are loaded by Claude Code every session.

| File | What it provides |
|------|-----------------|
| `.claude/rules/thinking.md` | Thinking framework, UI work process, iterative checkpoint pattern |
| `.claude/rules/workflow.md` | CHECKPOINT-based rules for API verification, visual verification, behavioral reporting |
| `.claude/rules/playwright.md` | Persistent Playwright CLI command reference |
| `.claude/rules/compaction.md` | What to preserve/drop during context compaction |

### Skills (User-Invoked)

| Skill | When to use | What it does |
|-------|-------------|--------------|
| `/setup-project` | Once, at project start | Asks questions, appends project-specific rules to CLAUDE.md |
| `/code-review` | After completing a feature | Calls code-reviewer agent + runs pint |
| `/ux-review` | After building/changing UI | Calls ux-reviewer agent to evaluate workflow, hierarchy, patterns |
| `/test-app` | When you want regression tests | Writes Pest feature tests + Livewire::test() |
| `/verify-ui` | During UI development | Opens browser, screenshots, evaluates, iterates |

### Agents (Called by Skills)

| Agent | Called by | Tools | Memory |
|-------|-----------|-------|--------|
| `code-reviewer` | `/code-review` | Read, Grep, Glob, Bash (read-only) | project |
| `ux-reviewer` | `/ux-review` | Read, Grep, Glob, Bash (read-only) | project |

---

## Repo Structure

```
claude-flow/
├── .claude/
│   ├── rules/
│   │   ├── thinking.md                ← Thinking framework + UI work process
│   │   ├── workflow.md                ← CHECKPOINT-based workflow rules
│   │   ├── playwright.md              ← Persistent Playwright CLI reference
│   │   └── compaction.md              ← Context compaction strategy
│   └── settings.local.json           ← Repo-specific permissions
├── agents/
│   ├── code-reviewer.md              ← Subagent for code quality review
│   └── ux-reviewer.md                ← Subagent for UX/workflow review
├── skills/
│   ├── setup-project/SKILL.md        ← /setup-project (one-time project config)
│   ├── code-review/SKILL.md          ← /code-review (calls agent + pint)
│   ├── ux-review/SKILL.md            ← /ux-review (calls agent, opens browser)
│   ├── verify-ui/SKILL.md            ← /verify-ui (build-verify loop)
│   └── test-app/SKILL.md             ← /test-app (Pest feature tests)
├── guidelines/
│   ├── architecture/conventions.md   ← Architecture, Eloquent, testing philosophy
│   ├── frontend/conventions.md       ← Flux-first, Livewire, component patterns, designer boundary
│   ├── frontend/design-system.md     ← Design tokens, components, archetypes
│   └── quality/anti-patterns.md      ← UX essentials + unique prohibitions
├── settings.json                     ← Permissions + environment (copy to .claude/)
├── settings.local.json               ← Hooks config (copy to .claude/)
└── README.md
```

---

## New Project Setup

### One-Time Prerequisites

```bash
# Playwright CLI (for visual verification during development)
npm install -g @playwright/cli@latest
playwright-cli install-browser

# Context7 MCP (global, for non-Laravel package docs -- Boost handles Laravel docs)
claude mcp add -s user context7 npx -y @upstash/context7-mcp@latest
```

### Per Project

```bash
# 1. Create project — the installer walks you through everything
#    Pick: Livewire starter, Pest, your DB, and say yes to Boost
laravel new project-name && cd project-name

# 2. Copy this repo into the project
cp -r ~/Code/claude-flow/agents/ .claude/agents/
cp -r ~/Code/claude-flow/skills/ .claude/skills/
cp -r ~/Code/claude-flow/.claude/rules/ .claude/rules/
cp -r ~/Code/claude-flow/guidelines/ .ai/guidelines/
cp ~/Code/claude-flow/settings.json .claude/settings.json
cp ~/Code/claude-flow/settings.local.json .claude/settings.local.json

# 3. Open Claude Code and configure project
claude
> /setup-project
```

### What Boost Already Handles (Don't Duplicate)

`laravel new` + `boost:install` gives you: CLAUDE.md with foundational rules, AI guidelines for all detected packages (version-specific), Boost MCP (15+ tools including `search-docs`), Herd MCP. This repo only adds what Boost doesn't generate.

---

## How the Layers Stack

```
laravel new
└── Livewire + Flux UI + Fortify + Tailwind + Pest

boost:install
├── CLAUDE.md (foundational rules -- always loaded)
├── .ai/guidelines/ (ecosystem guidelines -- always loaded)
├── Boost MCP (15+ tools including search-docs)
└── Herd MCP

THIS REPO
├── .ai/guidelines/ → always loaded (architecture, frontend, design system, quality)
├── .claude/rules/ → always loaded (thinking, workflow, playwright, compaction)
├── .claude/skills/ → user types /skill-name
├── .claude/agents/ → called by skills (with tool restrictions + memory)
├── .claude/settings.json → permissions + environment config
└── .claude/settings.local.json → hooks (Pint auto-format + dirty state warning)

/setup-project appends to CLAUDE.md
├── verify-ui mandate (always loaded, enforced every session)
├── design system decisions
├── compaction preservation instructions
└── project-specific context (domain, users, locale, currency)
```

## Hooks

The `settings.local.json` file configures two hooks:
- **PreToolUse** (Edit/Write): Warns when uncommitted changes exist before making more edits
- **PostToolUse** (Edit/Write): Auto-runs `vendor/bin/pint` on any PHP file CC writes or edits

Copy it to `.claude/settings.local.json` in each project. It's gitignored by default.

## Maintenance

**Shared files**: Edit here, push to GitHub, `cp` into active projects.
**Per project**: Boost updates via `php artisan boost:update`. Guidelines and rules here are opinions-only — API specifics come from Boost's `search-docs`.
