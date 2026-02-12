---
name: design-system
description: "Establish the design system for a new project or review the existing design system. Use when starting a new project, when the user says 'let's define the look and feel', or when UI inconsistencies arise. This skill ensures we define the visual language BEFORE building any pages."
---

## Purpose

Before building any UI, we establish the project's design system. This prevents the fragmented-styling problem where every page looks different because there's no shared foundation.

## When to Use This Skill

- Starting a new project's UI
- User says "let's define the design/look"
- Inconsistencies are appearing across pages
- Before a major UI refactor

## What We Define

### 1. Color Semantics

Map Flux UI color variants to meaning in THIS project.

Ask the user: "What's the personality of this app? Is it corporate/serious, friendly/casual, or minimal/clean?"

Then propose a color mapping:

```
Primary Action:    variant="primary" (Flux's default blue/indigo)
Success/Active:    color="lime" or color="green"
Warning/Pending:   color="yellow" or color="amber"
Danger/Error:      color="red"
Neutral/Info:      color="zinc"
Accent (optional): color="indigo" or color="cyan" (for highlights, links)
```

### 2. Component Density

Define the spacing rhythm for the project:

```css
/* In a doc or reference file, not actual CSS */
Between page sections:     gap-8 or space-y-8
Between cards/groups:      gap-6 or space-y-6
Between items in a group:  gap-4 or space-y-4
Between form fields:       gap-4 or space-y-4
Inside cards (padding):    p-4 (compact) or p-6 (normal)
```

### 3. Required Blade Components

Create these foundational components at project start:

```
resources/views/components/
├── ui/
│   ├── page-header.blade.php       # Title + subtitle + actions
│   ├── section-group.blade.php     # Heading + card wrapper
│   ├── stat-card.blade.php         # KPI metric card
│   ├── status-badge.blade.php      # Status indicator
│   ├── empty-state.blade.php       # Empty list/collection state
│   ├── price-display.blade.php     # Formatted currency
│   └── info-row.blade.php          # Label: Value pair
└── layout/
    └── app-shell.blade.php         # If not using starter kit layout
```

### 4. Page Templates

Define the standard layouts used across the app:

**List Page**: Page header → filters → content (cards) → pagination
**Detail Page**: Hero section → tabbed content
**Form Page**: Page header → section groups → footer actions
**Dashboard**: KPI row → primary content → secondary content
**Settings**: Navigation sidebar → content sections

### 5. Pattern Decisions

Document these decisions early:

- **Lists display as**: Cards (default) / Table (only when comparing data)
- **Forms use**: Section groups with `<flux:card>` / Stepper for 4+ groups
- **Modals for**: Confirmations, quick create, filters — NOT full forms
- **Toast for**: Success messages, errors — NOT warnings in the middle of flows
- **Empty states**: Always. Icon + message + CTA.

## Process

1. Ask the user about the app's personality and primary users
2. Propose the color mapping and density
3. Create the foundational Blade components
4. Document decisions in `.ai/guidelines/design-system.md`
5. Build the first page using these patterns as the reference implementation
6. All subsequent pages MUST follow the established patterns
