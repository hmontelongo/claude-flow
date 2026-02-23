---
name: verify-ui
description: Visually verify UI implementations during development. MUST be used after creating or modifying any user-facing page, component, or flow. This is part of building, not testing.
---

## Core Rule

**You MUST visually verify every UI change you make.** Writing code and assuming it works is not acceptable. You are a developer who sees their work running before calling it done.

## The Build-Verify-Iterate Loop

Every UI implementation follows this cycle:

```
1. IMPLEMENT -> Write the code (component, view, styles)
2. VERIFY    -> Open the page in the browser, see what it looks like
3. EVALUATE  -> Does it work? Does it look right? Edge cases?
4. FIX       -> Address any issues found
5. VERIFY    -> Screenshot again to confirm the fix
6. REPEAT    -> Until the implementation is solid
```

This is not optional. This is how you build UI.

## How to Verify: Playwright CLI

The persistent command reference lives in `.claude/rules/playwright.md`. Key workflow:

### 1. Open and Navigate

```bash
playwright-cli open https://your-app.test/login
```

### 2. Authenticate

Click the username field ref, fill the email, click the password field ref to focus it, then `type` the password (don't use `fill` on Flux password fields — see rules/playwright.md for details). Press Enter to submit.

Save auth state for reuse:
```bash
playwright-cli state-save auth.json
```

### 3. Navigate to the Page

```bash
playwright-cli goto https://your-app.test/dashboard
```

### 4. Capture and Evaluate

```bash
playwright-cli screenshot    # Visual state — read the PNG
playwright-cli snapshot      # Text content via accessibility tree
```

### 5. Interact and Test

Use refs from snapshot to interact: click buttons, fill forms, select options, press keys. Screenshot after each interaction to verify the result.

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

## Verification Checklists

### New Page
- Page loads without errors
- Layout and spacing look correct
- Data displays properly
- Screenshot taken for reference
- Mobile viewport checked (if relevant)

### New Form
- Form renders with all fields
- Labels and help text present
- Empty submission shows validation errors
- Valid submission shows success feedback
- Data actually saved (check via Tinker or DB query)
- Edit mode pre-fills correctly (if applicable)

### New Interactive Feature
- Primary action works
- Loading state visible during action
- Success/error feedback shown
- Page state correct after action

## When to Skip Verification

Almost never. Acceptable exceptions:
- Pure backend changes with no UI impact (model, migration, job)
- Changes to code that has no visual representation
- Refactoring that doesn't change behavior (but verify once after to be safe)

## The Minimum Standard

Before telling the user "I've implemented X":
- You have **seen it running** in the browser
- You have **verified the happy path** works
- You have **checked for obvious issues** (broken layout, missing data)
- You have **taken screenshots** to document what it looks like

If you cannot verify (browser not available, environment issue), **tell the user explicitly** that you haven't been able to visually verify and recommend they check it.
