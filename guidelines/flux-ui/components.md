# Flux UI Rules

## The Hierarchy (Always)

1. Flux component with built-in props (`variant`, `size`, `color`)
2. Blade component wrapping Flux — for reusable composed patterns
3. Livewire + minimal Tailwind — only when Flux has no component
4. Alpine.js — LAST resort, only for client-side micro-interactions Livewire can't handle

## Rules

**Component-first**: If a Flux composition appears 2+ times, extract it to `resources/views/components/`. Cards, badges, field groups — extract early.

**Flux props over Tailwind**: Use `variant="primary"`, `size="lg"`, `color="lime"` — never override Flux styling with Tailwind classes. Acceptable Tailwind on Flux components: margin (`mt-4`), width (`w-full`), responsive display (`hidden sm:block`).

**No Alpine for**: Form submissions, data fetching, server state, anything Flux already handles (modals, dropdowns, tabs, accordions). Alpine is acceptable for: copy-to-clipboard, client-only animations, keyboard shortcuts, drag-and-drop.

**Clean views**: No `@php` blocks in Blade. Data queries and formatting belong in Livewire computed properties or model accessors. Views describe WHAT to show, not HOW to compute it.

**Define design language first**: Before building any UI, run `/design-system` to establish color semantics, spacing rhythm, and foundational components. All pages follow the established patterns — no ad-hoc styling.

## Available Flux Components

**Forms**: `input`, `textarea`, `select`, `checkbox`, `radio`, `switch`, `autocomplete`, `date-picker`, `time-picker`, `file-upload`, `otp-input`, `slider`, `pillbox`, `editor`, `composer`
**Layout**: `card`, `separator`, `tabs`, `accordion`, `navbar`, `breadcrumbs`
**Data Display**: `table`, `badge`, `avatar`, `text`, `heading`, `icon`, `skeleton`, `chart`, `kanban`, `calendar`, `pagination`
**Feedback**: `modal`, `toast`, `tooltip`, `popover`, `callout`
**Actions**: `button`, `dropdown`, `command`, `context`
**Composed**: `field` (label + input + description + error), `profile`

When unsure about a component's API, use `search-docs` to check the installed version. For full patterns and examples, use `/ui-patterns`.
