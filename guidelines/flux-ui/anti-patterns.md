# Flux UI Anti-Patterns

## Things You Must NEVER Do

### 1. Raw HTML When Flux Has a Component
```blade
{{-- ❌ NEVER --}}
<button class="...">Click</button>          {{-- Use <flux:button> --}}
<input type="text" class="..." />            {{-- Use <flux:input> --}}
<select class="...">                         {{-- Use <flux:select> --}}
<table class="...">                          {{-- Use <flux:table> --}}
<div class="modal ...">                      {{-- Use <flux:modal> --}}
<div class="dropdown ...">                   {{-- Use <flux:dropdown> --}}
<div class="tabs ...">                       {{-- Use <flux:tabs> --}}
<span class="badge ...">                     {{-- Use <flux:badge> --}}
<textarea class="...">                       {{-- Use <flux:textarea> --}}
<input type="checkbox" class="..." />        {{-- Use <flux:checkbox> --}}
<input type="file" class="..." />            {{-- Use <flux:file-upload> --}}
```

### 2. Overriding Flux Styles with Tailwind
```blade
{{-- ❌ NEVER: Fighting the component's design --}}
<flux:button class="bg-blue-600 hover:bg-blue-700 rounded-xl px-8 py-4 text-lg font-bold shadow-lg">
    Save
</flux:button>

{{-- ✅ CORRECT: Use the component's API --}}
<flux:button variant="primary" size="lg">Save</flux:button>
```

**The only Tailwind classes acceptable on Flux components are:**
- Margin/spacing: `class="mt-4"` (positioning the component, not styling it)
- Width: `class="w-full"` (sizing the container)
- Display: `class="hidden sm:block"` (responsive visibility)

### 3. Building Custom Components That Flux Already Has
```blade
{{-- ❌ NEVER: Custom modal implementation --}}
<div x-data="{ open: false }">
    <button @click="open = true">Open</button>
    <div x-show="open" class="fixed inset-0 z-50 ...">
        <div class="bg-white rounded-lg shadow-xl p-6 ...">
            ...
        </div>
    </div>
</div>

{{-- ✅ CORRECT: Flux modal --}}
<flux:modal name="confirm-delete" class="max-w-sm">
    <div class="space-y-4">
        <flux:heading size="lg">Delete this record?</flux:heading>
        <flux:text>This action cannot be undone.</flux:text>
        <div class="flex gap-2 justify-end">
            <flux:button variant="ghost" x-on:click="$flux.modal.close('confirm-delete')">Cancel</flux:button>
            <flux:button variant="danger" wire:click="delete">Delete</flux:button>
        </div>
    </div>
</flux:modal>
```

### 4. Alpine.js for Things Livewire Handles
```blade
{{-- ❌ NEVER: Alpine state for server data --}}
<div x-data="{ items: [], loading: true }"
     x-init="fetch('/api/items').then(r => r.json()).then(d => { items = d; loading = false })">
    ...
</div>

{{-- ✅ CORRECT: Livewire --}}
{{-- In component: public $items; mount() { $this->items = Item::all(); } --}}
<div>
    @foreach($items as $item) ... @endforeach
</div>

{{-- ❌ NEVER: Alpine for form submission --}}
<form x-data="{ form: {} }" @submit.prevent="submitForm()">

{{-- ✅ CORRECT: Livewire --}}
<form wire:submit="save">
```

### 5. Inline PHP Logic in Views
```blade
{{-- ❌ NEVER --}}
@php
    $filtered = $items->filter(fn($i) => $i->price > 1000);
    $grouped = $filtered->groupBy('category');
    $stats = [
        'total' => $filtered->count(),
        'avg' => $filtered->avg('price'),
    ];
@endphp

{{-- ✅ CORRECT: Use computed properties in the Livewire component --}}
{{-- The view only references: $this->filteredItems, $this->stats --}}
```

### 6. Inconsistent Patterns Across Pages
If the items list uses cards, the orders list uses cards, and the users list uses cards — do NOT make the new "reports" page use a table. **Same data type = same pattern.**

### 7. Missing Essential UX Elements
Every interactive page MUST have:
- [ ] Empty state (when list has no items)
- [ ] Loading state (`wire:loading`)
- [ ] Error handling (validation messages or toast)
- [ ] Success feedback (toast after save/delete)
- [ ] Confirmation for destructive actions (modal)

### 8. Excessive Tailwind "Decorating"
```blade
{{-- ❌ NEVER: Over-styling to make things "look nice" --}}
<div class="bg-gradient-to-r from-blue-50 to-indigo-50 border border-blue-200 
            rounded-2xl shadow-lg p-6 hover:shadow-xl transition-all duration-300 
            transform hover:-translate-y-1">

{{-- ✅ CORRECT: Use Flux card, add minimal layout classes only --}}
<flux:card>
    {{-- Content --}}
</flux:card>
```

The project's visual identity comes from Flux's design system, not from custom Tailwind compositions. If every card looks different because each developer added their own gradient/shadow/border combo, the UI feels inconsistent.

### 9. Not Using `<flux:field>` for Form Fields
```blade
{{-- ❌ NEVER: Manual label + input + error --}}
<div>
    <label class="block text-sm font-medium text-zinc-700">Email</label>
    <flux:input wire:model="email" type="email" />
    @error('email') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
</div>

{{-- ✅ CORRECT: Flux field handles label, description, and errors --}}
<flux:field>
    <flux:label>Email</flux:label>
    <flux:input wire:model="email" type="email" />
    <flux:error name="email" />
</flux:field>
```

### 10. Vanilla JavaScript
```blade
{{-- ❌ NEVER --}}
<script>
    document.getElementById('myButton').addEventListener('click', function() { ... });
</script>

{{-- If you need client-side behavior, the order is: --}}
{{-- 1. Flux component (has it built in?) --}}
{{-- 2. Livewire (wire:click, wire:model) --}}
{{-- 3. Alpine.js (x-data, @click) — minimal, last resort --}}
{{-- 4. NEVER vanilla JS --}}
```

### 11. Breaking Accessibility
```blade
{{-- ❌ NEVER: Click handlers on non-interactive elements --}}
<div @click="doSomething()">Click me</div>
<span wire:click="save">Save</span>

{{-- ✅ CORRECT: Use semantic interactive elements --}}
<flux:button wire:click="save">Save</flux:button>
<button @click="doSomething()">Click me</button>
```
- Never use `tabindex` overrides on Flux components — they handle keyboard navigation.
- Never omit `alt` text on images. Empty `alt=""` is acceptable for decorative images only.
- Never remove focus outlines. Flux handles focus styling.
