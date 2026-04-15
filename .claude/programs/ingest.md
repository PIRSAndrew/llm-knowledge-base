# Ingest — Program

This file is the strategy layer for the `ingest` skill, following the autoresearch `program.md` pattern.
The skill reads this file before executing. Add lessons here — not to the skill file — unless the
execution steps themselves need to change.

## Current Strategy Notes
- The routing table is the single source of truth for where content lands. When in doubt, read it before routing.
- When content spans multiple topics, update all relevant articles rather than picking one.
- Frontmatter `updated` date must be bumped on every article touched.

## Known Patterns
<!-- Things that commonly appear and how to handle them correctly -->

## Edge Cases
<!-- Unusual situations encountered and how they were resolved -->

## Do Not
- Do not summarize raw saves — they must be verbatim for audit purposes
- Do not create new wiki articles for content that could fit in an existing one (prefer appending)
- Do not log scheduling-only content as a "decision" even if the message contains action words

## Lessons Log
| Date | Skill Run | Issue Observed | Lesson Added |
|------|-----------|---------------|--------------|
