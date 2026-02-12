# Flux UI Component Guidelines

## Core Philosophy

Flux UI is our design system. Every UI element should use Flux components as the foundation. Our job is NOT to style things from scratch — it's to compose Flux components into reusable patterns and only add custom styling when Flux genuinely can't handle it.

**The hierarchy, always:**
1. Use the Flux component with its built-in props (variant, size, color, etc.)
2. If you need a composed pattern (button + icon + badge), create a Blade component that wraps Flux
3. If Flux doesn't cover the pattern, use Livewire + minimal Tailwind
4. Alpine.js is a LAST resort — and only for client-side micro-interactions that Livewire can't handle

## Rule #1: Component-First Thinking

Every time you build UI, ask: **"Will this pattern appear more than once?"**

If yes (or even maybe) → Create a Blade component in `resources/views/components/`.

```blade
{{-- ❌ WRONG: Inline composition repeated across views --}}
<div class="flex items-center gap-3 rounded-lg border border-zinc-200 p-4 dark:border-zinc-700">
    <flux:icon name="home" class="text-zinc-400" />
    <div>
        <p class="text-sm font-medium">{{ $property->title }}</p>
        <p class="text-xs text-zinc-500">{{ $property->colonia }}</p>
    </div>
</div>

{{-- ✅ CORRECT: Blade component wrapping Flux --}}
<x-property-card :property="$property" />
```

### When to Create Components

| Signal | Action |
|--------|--------|
| Same Flux composition used 2+ times | Extract to `components/` |
| A visual pattern with variations (status badges, price displays) | Extract with props |
| A form field group with label + input + help text | Extract as field component |
| Any card-like grouping of data | Extract as a card component |

### Component Naming Convention
```
resources/views/components/
├── ui/                          # Generic, reusable across projects
│   ├── stat-card.blade.php      # KPI/metric display
│   ├── status-badge.blade.php   # Status indicators
│   ├── empty-state.blade.php    # Empty state with icon + CTA
│   ├── price-display.blade.php  # Formatted currency
│   └── info-row.blade.php       # Label: Value pair
├── property/                    # Domain-specific
│   ├── card.blade.php           # Property summary card
│   ├── detail-section.blade.php # Collapsible detail group
│   └── image-gallery.blade.php  # Image grid/carousel
└── layout/                      # Page-level patterns
    ├── page-header.blade.php    # Title + actions + breadcrumbs
    ├── filter-bar.blade.php     # Search + filter controls
    └── section-group.blade.php  # Grouped content with heading
```

## Rule #2: Flux Components Are the API — Not Tailwind

Use Flux's props before reaching for Tailwind classes. Flux components have been designed with consistent spacing, colors, and sizing.

```blade
{{-- ❌ WRONG: Fighting Flux with Tailwind --}}
<flux:button class="bg-blue-600 hover:bg-blue-700 text-white font-semibold px-6 py-3 rounded-lg">
    Guardar
</flux:button>

{{-- ✅ CORRECT: Use the prop --}}
<flux:button variant="primary">Guardar</flux:button>

{{-- ❌ WRONG: Custom badge styling --}}
<span class="inline-flex items-center rounded-full bg-green-100 px-2.5 py-0.5 text-xs font-medium text-green-800">
    Activo
</span>

{{-- ✅ CORRECT: Flux badge --}}
<flux:badge color="lime">Activo</flux:badge>
```

### Available Flux Components — Use These First

**Forms**: `input`, `textarea`, `select`, `checkbox`, `radio`, `switch`, `autocomplete`, `date-picker`, `time-picker`, `file-upload`, `otp-input`, `slider`, `pillbox`, `editor`, `composer`

**Layout**: `card`, `separator`, `tabs`, `accordion`, `navbar`, `breadcrumbs`

**Data Display**: `table`, `badge`, `avatar`, `text`, `heading`, `icon`, `skeleton`, `chart`, `kanban`, `calendar`, `pagination`

**Feedback**: `modal`, `toast`, `tooltip`, `popover`, `callout`

**Actions**: `button`, `dropdown`, `command`, `context`

**Composed**: `field` (wraps label + input + description + error), `profile`

### When Tailwind IS Appropriate
- Layout utilities: `flex`, `grid`, `gap-*`, `items-center`, `justify-between`
- Spacing between components: `space-y-*`, `gap-*`, `p-*`, `m-*`
- Responsive breakpoints: `sm:`, `md:`, `lg:`
- Conditional display: `hidden`, `sm:block`
- Width/height constraints: `max-w-*`, `w-full`

### When Tailwind is NOT Appropriate
- Coloring Flux components (use their `color`, `variant` props)
- Sizing Flux components (use their `size` prop)
- Typography on Flux components (they handle their own fonts)
- Border radius on Flux components (they handle their own rounding)
- Hover/focus states on Flux components (they handle their own states)

## Rule #3: Define the Design Language First

Before building any UI for a new project, establish the design tokens:

1. **Color usage**: Which Flux color variants map to which meaning?
   - `primary` → Main actions
   - `lime/green` → Success, active
   - `red` → Destructive, errors
   - `yellow/amber` → Warnings, pending
   - `zinc` → Neutral, disabled, secondary

2. **Component density**: How much spacing between elements?
   - Compact (settings, data-heavy) → `gap-2`, `space-y-2`, `p-3`
   - Normal (forms, content) → `gap-4`, `space-y-4`, `p-4`
   - Spacious (landing, marketing) → `gap-6`, `space-y-6`, `p-6`

3. **Card usage**: When to use cards vs flat sections?
   - Cards for grouped, self-contained content
   - Flat for flowing, continuous content

Once defined, stick to it everywhere. Do NOT ad-hoc style individual pages.

## Rule #4: No Alpine.js Unless Absolutely Necessary

**Decision tree for interactivity:**

```
Need interactivity?
├── Does Flux have a component for it? → USE FLUX (modal, dropdown, tabs, accordion)
├── Can Livewire handle it? → USE LIVEWIRE (wire:click, wire:model, wire:loading)
├── Is it a tiny UI-only toggle? → USE FLUX or Livewire (wire:click to toggle bool)
├── Is it a complex client-only interaction? → Check if Flux/Livewire can do it first
└── ONLY if none above work → Alpine.js with x-data, kept minimal
```

**Never use Alpine for:**
- Form submissions (use Livewire)
- Data fetching (use Livewire)
- State that the server needs to know about (use Livewire)
- Anything Flux already handles (dropdowns, modals, tabs, accordions)

**Alpine is acceptable for:**
- Copy-to-clipboard button
- Client-side-only animations/transitions
- Keyboard shortcut listeners
- Drag-and-drop (if not using Flux kanban)

## Rule #5: Clean Views — No PHP Logic in Blade

Views should be declarative. They describe WHAT to show, not HOW to compute it.

```blade
{{-- ❌ WRONG: PHP logic in the view --}}
@php
    $totalPrice = $properties->sum('price');
    $avgPrice = $properties->avg('price');
    $activeCount = $properties->where('status', 'active')->count();
    $formattedPrice = '$' . number_format($totalPrice, 0, '.', ',') . ' MXN';
@endphp
<div>{{ $formattedPrice }}</div>

{{-- ✅ CORRECT: Computed properties in Livewire component --}}
{{-- In the Livewire component class: --}}
{{-- #[Computed] --}}
{{-- public function totalPriceFormatted(): string { ... } --}}
<div>{{ $this->totalPriceFormatted }}</div>
```

### Use Livewire Volt for Simple Pages

When a page is a single component with simple logic, use Volt (single-file components):

```blade
{{-- resources/views/livewire/agent/dashboard.blade.php --}}
<?php

use Livewire\Volt\Component;
use Livewire\Attributes\Computed;

new class extends Component {
    #[Computed]
    public function stats(): array
    {
        return [
            'properties' => auth()->user()->properties()->count(),
            'collections' => auth()->user()->collections()->count(),
            'clients' => auth()->user()->clients()->count(),
        ];
    }
}; ?>

<div>
    <x-page-header title="Dashboard" />
    {{-- Clean, declarative view --}}
</div>
```

### Where Logic Belongs

| Type of Logic | Where It Goes |
|---------------|---------------|
| Data queries, aggregations | Livewire component (computed properties) |
| Format/display helpers | Blade component props, model accessors, or helpers |
| Business rules | Actions, Services, or Model methods |
| Conditional display | Simple `@if` in Blade (but NOT complex conditions) |
| Loops | `@foreach` in Blade (but data prep is in the component) |

## Available Components Reference

Before building anything, check if Flux has it. Use `search-docs` with queries like `["flux button", "flux table", "flux modal"]` to get the exact API for this project's installed version.
