# Anti-Patterns

Things you must never do. When in doubt, run `search-docs` to check if a component or pattern exists before building a custom alternative.

## UX Essentials

- **No inconsistent patterns across similar pages.** Same data type = same pattern. If items are cards, orders are cards, users are cards — the new page is also cards.
- **Every interactive page must have:**
  - Empty state (when list has no items)
  - Loading state (wire:loading)
  - Error handling (validation messages or toast)
  - Success feedback (toast after save/delete)
  - Confirmation for destructive actions (modal)

## Frontend (Not Covered Elsewhere)

- **No click handlers on non-interactive elements.** Use buttons and links, not `<div @click>` or `<span wire:click>`.
- **No excessive Tailwind decoration.** The project's visual identity comes from Flux's design system, not from custom gradient/shadow/border compositions on every card.

For the full frontend hierarchy, component rules, and Tailwind restrictions on Flux components, see `guidelines/frontend/conventions.md`.

For architecture and testing anti-patterns, see `guidelines/architecture/conventions.md`.
