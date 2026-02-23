---
name: code-review
description: "Review recent code changes for Laravel craftsmanship, simplicity, and convention adherence, then auto-format with Pint. Use when the user says 'review this code', 'code review', 'check my code', 'review before PR', or 'is this clean'. NOT for UX or visual review -- use /ux-review for that."
context: fork
allowed-tools: Bash(vendor/bin/pint *), Bash(git diff*), Bash(git log*), Bash(git status*)
---

Review code changes using the code-reviewer agent.

## Step 1: Code Review

If there are uncommitted changes, review those files.
If all changes are committed, review the diff between HEAD and the branch point from main.

Focus on files that were added or modified — skip deleted files.

Use the code-reviewer agent. Pass it the list of changed files.

## Step 2: Laravel Pint

After the code review finishes, run:
```bash
vendor/bin/pint --dirty
```

Report results from both steps.
