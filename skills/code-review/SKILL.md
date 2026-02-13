---
name: code-review
description: Run two-step code review (craftsmanship + formatting). Use after completing a feature, refactoring, or before a PR.
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
