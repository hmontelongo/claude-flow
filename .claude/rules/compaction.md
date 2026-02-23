# Compaction & Session Management

## When Compacting, Always Preserve

- List of modified files in this session
- Current task/plan state and progress
- Active decisions and constraints
- Errors being debugged and their context
- Project-specific context from CLAUDE.md (domain, users, locale, currency)

## What Can Be Safely Dropped

- Verbose file read outputs (the files still exist — re-read if needed)
- Large command outputs that were already analyzed
- Exploratory searches that didn't lead anywhere

## Session Hygiene

- Use `/clear` between unrelated tasks to start with a clean context
- Use `/compact [focus]` when context feels degraded — specify what to preserve
- Delegate research and exploration to subagents to keep main context clean
- Set `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50` in shell profile for earlier compaction
