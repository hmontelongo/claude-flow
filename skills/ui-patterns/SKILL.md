---
name: ui-patterns
description: "Reference UI/UX patterns and design system conventions for building interfaces. Load when creating any new page, component, or user flow. This skill defines HOW we build interfaces — the patterns, the composition rules, and the design philosophy. Use it before writing any view code."
---

## Design Philosophy

We don't build "CRUD pages." We build **workflows that help users accomplish goals**. Every screen answers: "What is the user trying to do right now, and what's the fastest path to get them there?"

### Core Principles

1. **No traditional table views unless data comparison is the goal.** We prefer cards, grouped rows, or aggregated displays. Tables are for comparing columns of data side-by-side. Most of our interfaces are not that.

2. **Progressive disclosure everywhere.** Show the minimum needed to decide. Reveal detail on interaction (expand, click-through, modal). A user should never feel overwhelmed on first load.

3. **Group aggressively.** If two pieces of information relate to each other, they live in the same visual container. If a section has more than 5-7 items, it should be subdivided.

4. **Hierarchy is king.** On every screen, one thing is most important. Design around that. Size, position, and visual weight communicate what matters.

5. **Consistency is a feature.** Same type of data → same pattern everywhere. If properties are shown as cards in search, they're cards in collections, they're cards in favorites.

## Pattern Library

### Pattern: Data List (Replaces Traditional Tables)

Instead of rows with columns, use **single-column card layouts** that aggregate information per item.

```blade
{{-- ❌ AVOID: Traditional table rows --}}
<flux:table>
    @foreach($properties as $property)
    <flux:table.row>
        <flux:table.cell>{{ $property->title }}</flux:table.cell>
        <flux:table.cell>{{ $property->price }}</flux:table.cell>
        <flux:table.cell>{{ $property->colonia }}</flux:table.cell>
        <flux:table.cell>{{ $property->bedrooms }}</flux:table.cell>
    </flux:table.row>
    @endforeach
</flux:table>

{{-- ✅ PREFER: Card-based list with aggregated info --}}
<div class="space-y-3">
    @foreach($properties as $property)
    <x-property-card :property="$property" wire:key="property-{{ $property->id }}" />
    @endforeach
</div>
```

The card aggregates: image thumbnail + title + key metrics + status + quick actions — all in one row or compact card. The user sees everything they need without parsing a table.

**When tables ARE appropriate:**
- Comparing many items across the same attributes (e.g., pricing comparison)
- Admin data management where bulk selection is needed
- Log/audit entries where chronological scanning is the goal

Even then, prefer Flux's `<flux:table>` with minimal columns and good use of badges/icons to reduce text noise.

### Pattern: Page Layout

Every page follows this structure:

```blade
<div>
    {{-- 1. Page Header: Title + Primary Action --}}
    <div class="flex items-center justify-between mb-6">
        <div>
            <flux:heading size="xl">{{ $title }}</flux:heading>
            @if($subtitle)
                <flux:text class="mt-1">{{ $subtitle }}</flux:text>
            @endif
        </div>
        <div class="flex items-center gap-2">
            {{-- Secondary actions first, primary last (rightmost = most important) --}}
            <flux:button variant="ghost" icon="funnel">Filtros</flux:button>
            <flux:button variant="primary" icon="plus">Nueva Propiedad</flux:button>
        </div>
    </div>

    {{-- 2. Filters/Search (if applicable) --}}
    {{-- Inline for 1-3 filters, collapsible/modal for more --}}

    {{-- 3. Content Area --}}
    {{-- The main content, using the appropriate pattern --}}

    {{-- 4. Pagination or Infinite Scroll --}}
</div>
```

### Pattern: Section Groups (Settings, Detail Pages)

Group related fields/info into sections with clear headings.

```blade
<div class="space-y-8">
    {{-- Each section is a visual group --}}
    <section>
        <div class="mb-4">
            <flux:heading size="lg">Información General</flux:heading>
            <flux:text size="sm" class="mt-1">Datos básicos de la propiedad</flux:text>
        </div>
        <flux:card>
            <div class="space-y-4">
                {{-- Fields inside the card --}}
                <flux:field>
                    <flux:label>Título</flux:label>
                    <flux:input wire:model="title" />
                </flux:field>
                {{-- ... --}}
            </div>
        </flux:card>
    </section>

    <section>
        <div class="mb-4">
            <flux:heading size="lg">Ubicación</flux:heading>
        </div>
        <flux:card>
            {{-- Location fields --}}
        </flux:card>
    </section>
</div>
```

### Pattern: Stepper / Multi-Step Flow

For creation or onboarding flows with 3+ logical groups.

```blade
{{-- Use Flux tabs as a visual stepper --}}
<flux:tabs variant="segmented" wire:model="currentStep">
    <flux:tab name="basics" icon="home">Datos</flux:tab>
    <flux:tab name="location" icon="map-pin">Ubicación</flux:tab>
    <flux:tab name="media" icon="photo">Fotos</flux:tab>
    <flux:tab name="review" icon="check">Revisar</flux:tab>
</flux:tabs>

<div class="mt-6">
    @if($currentStep === 'basics')
        {{-- Step 1 content --}}
    @elseif($currentStep === 'location')
        {{-- Step 2 content --}}
    @endif
</div>

<div class="flex justify-between mt-6">
    <flux:button variant="ghost" wire:click="previousStep" :disabled="$currentStep === 'basics'">
        Anterior
    </flux:button>
    @if($currentStep === 'review')
        <flux:button variant="primary" wire:click="submit">Publicar</flux:button>
    @else
        <flux:button variant="primary" wire:click="nextStep">Siguiente</flux:button>
    @endif
</div>
```

### Pattern: Search + Filter Interface

```blade
{{-- Search bar: always visible, prominent --}}
<div class="mb-4">
    <flux:input
        wire:model.live.debounce.300ms="search"
        placeholder="Buscar propiedades..."
        icon="magnifying-glass"
        clearable
    />
</div>

{{-- Quick filters: inline for the 2-3 most used --}}
<div class="flex items-center gap-2 mb-4">
    <flux:select wire:model.live="propertyType" placeholder="Tipo" size="sm">
        <flux:select.option value="casa">Casa</flux:select.option>
        <flux:select.option value="departamento">Departamento</flux:select.option>
    </flux:select>

    {{-- More filters in a modal/popover for everything else --}}
    <flux:button variant="ghost" size="sm" icon="adjustments-horizontal" wire:click="$toggle('showFilters')">
        Más filtros
        @if($activeFilterCount > 0)
            <flux:badge size="sm" color="indigo" inset="right">{{ $activeFilterCount }}</flux:badge>
        @endif
    </flux:button>
</div>

{{-- Active filter pills (removable) --}}
@if($activeFilterCount > 0)
<div class="flex flex-wrap gap-1 mb-4">
    @foreach($activeFilters as $filter)
        <flux:badge variant="pill" removable wire:click="removeFilter('{{ $filter['key'] }}')">
            {{ $filter['label'] }}
        </flux:badge>
    @endforeach
    <flux:button variant="ghost" size="xs" wire:click="clearAllFilters">Limpiar todo</flux:button>
</div>
@endif

{{-- Results --}}
<div class="space-y-3">
    @forelse($properties as $property)
        <x-property-card :property="$property" wire:key="prop-{{ $property->id }}" />
    @empty
        <x-empty-state
            icon="magnifying-glass"
            title="Sin resultados"
            description="Intenta con otros filtros o términos de búsqueda"
        />
    @endforelse
</div>

{{-- Infinite scroll trigger --}}
@if($hasMorePages)
<div x-intersect="$wire.loadMore()" class="py-4">
    <div wire:loading wire:target="loadMore" class="flex justify-center">
        <flux:icon name="arrow-path" class="animate-spin text-zinc-400" />
    </div>
</div>
@endif
```

### Pattern: Dashboard / KPI View

```blade
{{-- KPI row: 3-4 stats maximum --}}
<div class="grid grid-cols-2 gap-4 mb-6 sm:grid-cols-4">
    <x-stat-card label="Propiedades" :value="$this->propertyCount" icon="home" />
    <x-stat-card label="Colecciones" :value="$this->collectionCount" icon="folder" />
    <x-stat-card label="Clientes" :value="$this->clientCount" icon="users" />
    <x-stat-card label="Compartidas" :value="$this->sharedCount" icon="share" />
</div>

{{-- Primary content: the thing the user came here to do --}}
<section class="mb-6">
    <div class="flex items-center justify-between mb-4">
        <flux:heading size="lg">Actividad Reciente</flux:heading>
        <flux:button variant="ghost" size="sm" href="{{ route('agent.activity') }}">Ver todo</flux:button>
    </div>
    {{-- Activity feed or recent items --}}
</section>

{{-- Secondary content --}}
<section>
    <div class="flex items-center justify-between mb-4">
        <flux:heading size="lg">Colecciones Recientes</flux:heading>
    </div>
    {{-- Recent collections --}}
</section>
```

### Pattern: Empty States

**Every list must have an empty state.** No exceptions.

```blade
{{-- Create as a reusable component: resources/views/components/empty-state.blade.php --}}
@props(['icon' => 'inbox', 'title', 'description' => null, 'actionLabel' => null, 'actionRoute' => null])

<div class="flex flex-col items-center justify-center py-12 text-center">
    <div class="flex items-center justify-center w-12 h-12 mb-4 rounded-full bg-zinc-100 dark:bg-zinc-800">
        <flux:icon :name="$icon" variant="outline" class="text-zinc-400" />
    </div>
    <flux:heading size="sm">{{ $title }}</flux:heading>
    @if($description)
        <flux:text size="sm" class="mt-1 max-w-sm">{{ $description }}</flux:text>
    @endif
    @if($actionLabel)
        <flux:button variant="primary" size="sm" class="mt-4" :href="$actionRoute">
            {{ $actionLabel }}
        </flux:button>
    @endif
</div>
```

### Pattern: Detail / Show Page

Instead of a flat page with all data, use **collapsible sections** or **tabs** to organize detail.

```blade
<div>
    {{-- Hero area: The most critical info at a glance --}}
    <div class="flex gap-6 mb-6">
        {{-- Image/thumbnail --}}
        <div class="shrink-0">
            {{-- Cover image --}}
        </div>
        {{-- Key info: title, price, location, status --}}
        <div class="flex-1">
            <flux:heading size="xl">{{ $property->title }}</flux:heading>
            <x-price-display :amount="$property->price" class="mt-1" />
            <flux:text class="mt-2">{{ $property->full_address }}</flux:text>
            <div class="flex gap-2 mt-3">
                <x-status-badge :status="$property->status" />
                <flux:badge>{{ $property->property_type_label }}</flux:badge>
            </div>
        </div>
        {{-- Actions --}}
        <div class="flex flex-col gap-2 shrink-0">
            <flux:button variant="primary" icon="share">Compartir</flux:button>
            <flux:button variant="ghost" icon="pencil">Editar</flux:button>
        </div>
    </div>

    {{-- Tabbed or accordion detail sections --}}
    <flux:tabs wire:model="activeTab">
        <flux:tab name="details" icon="list-bullet">Detalles</flux:tab>
        <flux:tab name="images" icon="photo">Fotos</flux:tab>
        <flux:tab name="activity" icon="clock">Historial</flux:tab>
    </flux:tabs>

    <div class="mt-4">
        {{-- Tab content --}}
    </div>
</div>
```

## Before Building Any Page

Run through this checklist:

1. **What is the user's goal?** (one sentence)
2. **What pattern fits?** (list, detail, form, dashboard, stepper)
3. **What's the primary action?** (the ONE thing most users will do)
4. **What can be hidden?** (secondary info, advanced options, rare actions)
5. **What Flux components do we need?** (search-docs if unsure about API)
6. **Does a similar page exist?** (check for existing patterns to follow)
7. **What Blade components do we need to create?** (reusable pieces)
8. **Empty state designed?** (what does the user see with no data?)
9. **Loading states planned?** (wire:loading on every async action)
10. **Error handling planned?** (validation messages, toast notifications)
