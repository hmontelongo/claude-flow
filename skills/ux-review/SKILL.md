---
name: ux-review
description: "Review a page or user flow for UX quality -- workflow logic, visual hierarchy, consistency, and user-friendliness. Use when the user says 'review the UX', 'how does this look', 'check the UI', 'is this page good', or 'review this flow'. Takes screenshots and evaluates the live page. NOT for code quality -- use /code-review for that."
argument-hint: "[page or route to review]"
context: fork
---

Review the UX of a recently built or modified interface using the ux-reviewer agent.

## What to Review

If the user specified a page or component, review that.
If not, look at recently modified view files (`.blade.php` in `resources/views/`) and Livewire components to determine what was just built.

## Process

1. Open the page in the browser to see the current state (use Playwright CLI)
2. Pass the screenshot and the relevant view/component code to the ux-reviewer agent
3. The ux-reviewer evaluates: workflow logic, hierarchy, progressive disclosure, empty states, mobile considerations
4. Report findings back with specific, actionable suggestions

## After the Review

Ask the user which suggestions they want to implement. Do not auto-implement UX changes — they involve product decisions.
