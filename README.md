# claude-flow

Shared Claude Code configuration for Laravel + Livewire + Flux UI projects.
Goes **on top of** what Laravel Boost already generates.

---

## Features

- **Guidelines** (always loaded): Architecture conventions, frontend patterns, anti-patterns -- opinions only, no API specifics that go stale
- **Skills** (user-invoked): Code review, UX review, visual verification, test writing, project setup
- **Agents** (called by skills): Specialized subagents with tool restrictions and persistent project memory
- **Permissions allowlist**: Pre-approved commands (artisan, pint, pest, npm, composer, git read-only, playwright) to reduce prompt friction
- **Persistent Playwright reference**: `.claude/rules/playwright.md` solves the "re-learns CLI every session" problem
- **Compaction strategy**: `.claude/rules/compaction.md` tells CC what to preserve and what to drop during context compaction

---

## How Everything Activates

### Guidelines (Always On)

Files in `.ai/guidelines/` are loaded by Boost into CC's context every session.

| File | What it enforces |
|------|-----------------|
| `guidelines/architecture/conventions.md` | Architecture layers, Eloquent principles, return types, Form Requests, testing philosophy |
| `guidelines/frontend/conventions.md` | Flux-first hierarchy, component extraction, Livewire conventions, wire:model, page structure |
| `guidelines/quality/anti-patterns.md` | Explicit prohibitions for frontend, UX, architecture, and testing |

### Rules (Always On)

Files in `.claude/rules/` are loaded by Claude Code every session.

| File | What it provides |
|------|-----------------|
| `.claude/rules/playwright.md` | Persistent Playwright CLI command reference |
| `.claude/rules/compaction.md` | What to preserve/drop during context compaction |
| `.claude/rules/workflow.md` | Session workflow rules (verify APIs, check browser, read CLAUDE.md) |

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
│   │   ├── playwright.md              ← Persistent Playwright CLI reference
│   │   ├── compaction.md              ← Context compaction strategy
│   │   └── workflow.md                ← Session workflow rules
│   └── settings.local.json           ← Repo-specific permissions (unchanged)
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
│   ├── frontend/conventions.md       ← Flux-first, Livewire, component patterns
│   └── quality/anti-patterns.md      ← Explicit prohibitions
├── settings.json                     ← Permissions allowlist (copy to .claude/)
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
# 1. Create project (choose Livewire starter, Pest, your DB)
laravel new project-name && cd project-name

# 2. Install Boost (generates CLAUDE.md, guidelines, MCP servers)
composer require laravel/boost --dev && php artisan boost:install

# 3. Copy this repo into the project
cp -r ~/Code/claude-flow/agents/ .claude/agents/
cp -r ~/Code/claude-flow/skills/ .claude/skills/
cp -r ~/Code/claude-flow/.claude/rules/ .claude/rules/
cp -r ~/Code/claude-flow/guidelines/ .ai/guidelines/
cp ~/Code/claude-flow/settings.json .claude/settings.json
cp ~/Code/claude-flow/settings.local.json .claude/settings.local.json

# 4. Open Claude Code and configure project
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
├── .ai/guidelines/ → always loaded (architecture, frontend, quality)
├── .claude/rules/ → always loaded (playwright, compaction, workflow)
├── .claude/skills/ → user types /skill-name
├── .claude/agents/ → called by skills (with tool restrictions + memory)
├── .claude/settings.json → permissions allowlist
└── .claude/settings.local.json → hooks (auto-format with Pint)

/setup-project appends to CLAUDE.md
├── verify-ui mandate (always loaded, enforced every session)
├── git safety rules
├── design system decisions
├── compaction preservation instructions
└── project-specific context (domain, users, locale, currency)
```

## Hooks

The `settings.local.json` file configures a PostToolUse hook that auto-runs `vendor/bin/pint` on any PHP file CC writes or edits. Copy it to `.claude/settings.local.json` in each project. It's gitignored by default.

## Maintenance

**Shared files**: Edit here, push to GitHub, `cp` into active projects.
**Per project**: Boost updates via `php artisan boost:update`. Guidelines and rules here are opinions-only — API specifics come from Boost's `search-docs`.
