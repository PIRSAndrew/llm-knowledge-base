# KB Lint — Program

This file is the strategy layer for the `kb-lint` skill, following the autoresearch `program.md` pattern.
The skill reads this file before executing. Add lessons here — not to the skill file — unless the
execution steps themselves need to change.

## Current Strategy Notes
- Run all 10 checks even if early ones find many issues — a complete picture every time.
- Severity calibration: RED = blocks KB usability (broken links, missing frontmatter on queried articles). YELLOW = degrades quality but doesn't block (stale content, routing gaps). GREEN = informational.
- A lint pass with zero issues is suspicious — check that the scan actually ran on all articles.

## Known Patterns
<!-- Things that commonly appear and how to handle them correctly -->

## Edge Cases
<!-- Unusual situations encountered and how they were resolved -->

## Do Not
- Do not flag `.gitkeep` files as missing frontmatter — they are intentional placeholders
- Do not flag the article-template as stale — it has no decisions by design
- Do not flag `[[backlinks]]` that are inside HTML comments as broken links

## Lessons Log
| Date | Skill Run | Issue Observed | Lesson Added |
|------|-----------|---------------|--------------|
