---
description: Run UX review on a page or flow using the ux-reviewer agent. Use after implementing or modifying any user-facing interface.
---

Review the UX of a recently built or modified interface using the ux-reviewer agent.

## What to Review

If the user specified a page or component, review that.
If not, look at recently modified view files (`.blade.php` in `resources/views/`) and Livewire components to determine what was just built.

## Process

1. Open the page in the browser to see the current state (use the verify-ui skill)
2. Pass the screenshot and the relevant view/component code to the ux-reviewer agent
3. The ux-reviewer evaluates: workflow logic, hierarchy, progressive disclosure, empty states, mobile considerations
4. Report findings back with specific, actionable suggestions

## After the Review

Ask the user which suggestions they want to implement. Do not auto-implement UX changes — they involve product decisions.
