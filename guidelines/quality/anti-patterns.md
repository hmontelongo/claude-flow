# Anti-Patterns

Things you must never do. When in doubt, run `search-docs` to check if a component or pattern exists before building a custom alternative.

## Frontend

- **No raw HTML when Flux has a component.** Always check `search-docs` first — buttons, inputs, selects, tables, modals, dropdowns, tabs, badges, textareas, checkboxes, file uploads all have Flux components.
- **No Tailwind overriding Flux styling.** Never apply colors, typography, borders, border-radius, or hover/focus states via Tailwind on Flux components. Use the component's built-in props.
- **No Alpine.js for things Flux/Livewire handle.** Alpine is not for form submissions, data fetching, server state, modals, dropdowns, tabs, or accordions. Alpine is acceptable for: copy-to-clipboard, client-only animations, keyboard shortcuts, drag-and-drop.
- **No inline PHP logic in Blade views.** No `@php` blocks. Data queries and formatting belong in computed properties or model accessors.
- **No vanilla JavaScript.** The hierarchy is: Flux -> Livewire -> Alpine.js -> never vanilla JS.
- **No click handlers on non-interactive elements.** Use buttons and links, not `<div @click>` or `<span wire:click>`.
- **No excessive Tailwind decoration.** The project's visual identity comes from Flux's design system, not from custom gradient/shadow/border compositions on every card.

## UX Essentials

- **No inconsistent patterns across similar pages.** Same data type = same pattern. If items are cards, orders are cards, users are cards — the new page is also cards.
- **Every interactive page must have:**
  - Empty state (when list has no items)
  - Loading state (wire:loading)
  - Error handling (validation messages or toast)
  - Success feedback (toast after save/delete)
  - Confirmation for destructive actions (modal)

## Architecture

- **No Repository classes** wrapping Eloquent — Eloquent IS the repository
- **No Interfaces** without 2+ implementations
- **No Services** for trivial operations — use an Action or keep it in the controller
- **No scattered queries** outside of model scopes — query logic belongs on the model
- **No bloated controllers** — 10-15 lines per method, 7 resource methods max

## Testing

- **No Pest browser tests.** No `visit()`, no `assertNoJavascriptErrors()`. Use Livewire::test() and HTTP tests for application testing. Use Playwright CLI for visual verification.
- **No tests that mock everything.** Mock only external APIs you don't control. Use RefreshDatabase for real DB calls.
- **No tests that pass when the code is broken.** Mutation testing mindset — if you break the code, at least one test should fail. If no test fails, the test suite is insufficient.
