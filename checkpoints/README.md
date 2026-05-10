# Checkpoints

Use this folder for compact resume points, not full audits.

## Purpose

Checkpoints exist to save tokens when work pauses mid-phase. They should tell the next session exactly where to resume without rereading every audit, roadmap, or long product document.

## Rules

- Update `current.md` only when the user asks to save a checkpoint, resume point, or where development left off.
- Read `current.md` first when the user asks to resume from a previous session.
- Keep entries short, factual, and action-oriented.
- Do not duplicate full audit history.
- Do not include secrets, API keys, private logs, or long diffs.

## Recommended Format

```md
# Current Checkpoint

Last updated: YYYY-MM-DD

## Phase

- Current phase:
- Status:

## Completed Since Last Checkpoint

- Item 1.
- Item 2.

## Current Working State

- App/code state:
- Database/Supabase state:
- Docs state:

## Next Actions

1. Next concrete step.
2. Next concrete step.

## Known Blockers Or Risks

- Blocker/risk.

## Files To Read First

- `path/to/file`
```
