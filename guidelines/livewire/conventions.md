# Livewire 4 Conventions

## Defaults

**Single-file components are the default.** `php artisan make:livewire` creates a single-file, class-based component. This is the unified approach in Livewire 4.

**Full-page components for pages, nested for reusable pieces.** Pages route directly to Livewire components. Search bars, live stat cards, dropdowns — these are nested components.

## State

**Keep state minimal.** Livewire serializes all public properties every request. Pass primitives, not Eloquent models. Need a model? Pass the ID, resolve in a computed property.

**Computed properties (`#[Computed]`) for all derived data.** Cached within request lifecycle. Use for database queries and calculations. Never assign query results to public properties. Use `persist: true` for expensive, stable queries.

## wire:model Conventions

- `wire:model` (deferred, default) — most form fields
- `wire:model.live` — ONLY for search/filter needing instant feedback, always with `.debounce.300ms`
- `wire:model.blur` — validation feedback on focus loss

**Default to deferred.** `.live` on every field is a performance anti-pattern.

## Forms

**Form objects for 5+ field forms.** Colocates validation rules with form data. Keeps the component class clean.

For simpler forms, use `#[Validate]` attributes on individual properties.

## Loading States

Livewire 4 adds `data-loading` automatically. Use `data-[loading]:opacity-50` with Tailwind for simple feedback. For targeted loading: `wire:loading` with `wire:target`.

## Component Communication

- **Parent → Child**: Props
- **Child → Parent**: `$this->dispatch('event-name')`
- **Siblings**: Through parent events
- **Global**: `$this->dispatch('event-name')` without scoping

## Performance Rules

- **No Eloquent models in public properties** — pass IDs, resolve in computed
- **No `wire:model.live` on every field** — deferred is the default for a reason
- **`wire:key` on every `@foreach` item** — prevents DOM diffing bugs
- **`#[Lazy]` on below-fold and tabbed components** — defer rendering until visible
- **`$this->skipRender()`** when a method doesn't need to re-render the view

## PHP 8.4 Property Hooks

Use `set =>` hooks for value normalization: `set => max(1, $value)` replaces Livewire `updating` hooks.
