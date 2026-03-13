# Workflow Rules

## Before Implementation

CHECKPOINT — Before implementing any Livewire or Flux pattern: run `search-docs` to verify the API. Not from memory. Not assumed. Actually verified.

CHECKPOINT — Before making any product decision: read CLAUDE.md for project-specific context (domain, users, locale, currency). The answer changes per project.

CHECKPOINT — Before integrating any external API: read official documentation (`search-docs` for Laravel ecosystem, Context7 MCP for others). Never rely on assumptions.

## After Implementation

CHECKPOINT — After modifying user-facing code: visually verify in the browser. Screenshots or Playwright snapshots — not "it should work."

CHECKPOINT — When reporting changes: describe BEHAVIORAL impact (what changed for the user, what it looks like), not file lists. "Modified 3 files" tells the user nothing.

CHECKPOINT — Before reporting a fix as done: confirm the actual result. For CSS/visual issues, verify the root cause (overflow, z-index, stacking context) before implementing fixes. Don't guess.
