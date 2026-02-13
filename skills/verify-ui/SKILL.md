---
name: verify-ui
description: Visually verify UI implementations during development. MUST be used after creating or modifying any user-facing page, component, or flow. This is part of building, not testing.
---

## Core Rule

**You MUST visually verify every UI change you make.** Writing code and assuming it works is not acceptable. You are a developer who sees their work running before calling it done.

## The Build-Verify-Iterate Loop

Every UI implementation follows this cycle:

```
1. IMPLEMENT → Write the code (component, view, styles)
2. VERIFY    → Open the page in the browser, see what it looks like
3. EVALUATE  → Does it work? Does it look right? Edge cases?
4. FIX       → Address any issues found
5. VERIFY    → Screenshot again to confirm the fix
6. REPEAT    → Until the implementation is solid
```

This is not optional. This is how you build UI.

## How to Verify

### Using Playwright CLI (Primary Method)

Requires: `npm install -g @playwright/cli@latest && playwright-cli install-browser`

```bash
# 1. Open the page in a headed browser
playwright-cli open https://your-app.test/dashboard

# 2. Take a snapshot (returns page structure with element refs for interaction)
playwright-cli snapshot

# 3. Take a screenshot (saves visual capture to disk)
playwright-cli screenshot

# 4. Interact with the page (use refs from snapshot)
playwright-cli click ref15          # Click a button by ref
playwright-cli fill ref21 "test"    # Fill an input by ref
playwright-cli select ref8 "option" # Select a dropdown option
playwright-cli press Enter          # Press a key
playwright-cli screenshot           # Capture the result

# 5. Check different states
# Navigate to empty state
# Try submitting without required fields
# Check with long text content
# Check loading states

# 6. Check for JS errors
playwright-cli console
```

**Key commands reference:**

| Command | What it does |
|---------|-------------|
| `open [url]` | Open browser, optionally navigate to URL |
| `goto <url>` | Navigate to a URL |
| `snapshot` | Capture page structure with element refs |
| `screenshot [ref]` | Screenshot the page or a specific element |
| `click <ref>` | Click an element |
| `fill <ref> <text>` | Fill text into an input |
| `select <ref> <val>` | Select a dropdown option |
| `type <text>` | Type text into the focused element |
| `press <key>` | Press a keyboard key |
| `console` | Show console messages (check for JS errors) |
| `state-save [file]` | Save auth state for reuse |
| `state-load <file>` | Load saved auth state |

### Using Pest 4 Browser Visit (Alternative)

When you need Laravel auth context:

```php
// Quick verification script (not a permanent test)
$this->actingAs($user);
$page = visit('/dashboard');
$page->screenshot('verify-dashboard');
```

### Using Boost Browser Logs

After visiting a page, check for JavaScript errors:
```
Use the browser-logs tool to check for recent errors
```

## What to Verify

### On Every Page Implementation

1. **Does it render?** — No white screen, no PHP errors, no JS errors
2. **Does it look right?** — Layout matches the intended design, spacing is consistent
3. **Does the data display correctly?** — Real data shows up, formatting is correct
4. **Do interactions work?** — Buttons click, forms submit, modals open

### On Form Implementations

5. **Validation** — Submit empty form, check error messages appear
6. **Success path** — Fill correctly, submit, verify success feedback (toast/redirect)
7. **Edge cases** — Very long text, special characters, zero values

### On List/Search Implementations

8. **Empty state** — What does it look like with no data?
9. **With data** — Do items render correctly?
10. **Pagination/scroll** — Does loading more work?
11. **Filters** — Do filters actually filter? Do they clear?

### On Detail Pages

12. **All sections render** — Tabs work, expandable sections expand
13. **Actions work** — Edit, delete, share buttons function
14. **Missing data** — What happens when optional fields are null?

## Verification Checklist by Feature Type

### New Page
```
□ Page loads without errors
□ Layout and spacing look correct
□ Data displays properly
□ Screenshot taken for reference
□ Mobile viewport checked (if relevant)
```

### New Form
```
□ Form renders with all fields
□ Labels and help text present
□ Empty submission → validation errors appear
□ Valid submission → success feedback
□ Data actually saved (check via Tinker or DB query)
□ Edit mode pre-fills correctly (if applicable)
```

### New Interactive Feature
```
□ Primary action works
□ Loading state visible during action
□ Success/error feedback shown
□ Page state correct after action
□ No console errors
```

## When to Skip Verification

Almost never. But these are acceptable exceptions:
- Pure backend changes with no UI impact (model, migration, job)
- Changes to code that has no visual representation
- Refactoring that doesn't change behavior (but verify once after to be safe)

## Authentication During Verification

### With Playwright CLI
Two options for authenticated pages:

**Option A: Manual login (interactive)**
1. `playwright-cli open https://your-app.test/login`
2. Log in manually in the browser
3. `playwright-cli state-save auth.json` — save the session
4. Future sessions: `playwright-cli state-load auth.json`

**Option B: Ask the user to log in**
1. Navigate to the login page
2. Ask the user to log in manually (Playwright shows a real browser)
3. Session persists for the rest of the Playwright session

### With Pest Browser Visit
Use `actingAs()` — no login dance needed:
```php
$this->actingAs(User::factory()->create());
$page = visit('/dashboard');
```

## The Minimum Standard

Before telling the user "I've implemented X":
- You have **seen it running** in the browser
- You have **verified the happy path** works
- You have **checked for obvious issues** (broken layout, missing data, console errors)
- You have **taken screenshots** to document what it looks like

If you cannot verify (browser not available, environment issue), **tell the user explicitly** that you haven't been able to visually verify and recommend they check it.
