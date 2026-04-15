---
description: Evaluate a recent skill run and update its program.md with lessons learned. Run this after any skill execution to close the autoresearch feedback loop.
argument-hint: [ingest | kb-lint | ingest-codebase | all]
---

# Skill Improve

You are running the skill improvement loop — Karpathy's autoresearch `program.md` pattern applied to KB skills.

The analogy:
- `.claude/commands/{skill}.md` = `train.py` (execution steps, stable)
- `.claude/programs/{skill}.md` = `program.md` (strategy layer, where learning accumulates)
- This command = the evaluation agent that measures output and updates the strategy

## Step 0: Determine Scope

Parse `$ARGUMENTS`:
- A skill name (`ingest`, `kb-lint`, `ingest-codebase`): evaluate that skill only
- `all`: evaluate all skills
- No argument: ask the user which skill was just run

## Step 1: Read the Skill and Its Program File

For each skill being evaluated, read:
1. `.claude/commands/{skill}.md` — the current execution steps
2. `.claude/programs/{skill}.md` — the accumulated strategy and lessons

If `.claude/programs/{skill}.md` doesn't exist, create it using the template in Step 5 before continuing.

## Step 2: Gather Evidence from the Most Recent Run

Look for evidence of the most recent skill execution. Check in order:
1. **Recent log entry**: Read `knowledge-base/log.md` — find the most recent entry matching this skill
2. **Recent raw files**: Check the relevant raw directory for files created today or yesterday
3. **Recent wiki updates**: Check git log or file modification dates on wiki articles
4. Ask the user: "Can you describe any quality issues or surprises from the last run?" (brief, optional)

## Step 3: Evaluate Quality

Score the recent run across these dimensions. Be specific — vague findings don't improve the skill.

**For `ingest`:**
- Routing accuracy: Did content get mapped to the right wiki articles? Any misroutes?
- Filter quality: Did it catch real decisions? Any false positives (scheduling, greetings ingested)?
- Completeness: Were any obvious decisions missed?
- Raw save quality: Was the raw file well-formatted and complete?
- Log entry quality: Was the log entry accurate and parseable?

**For `kb-lint`:**
- Coverage: Did it check all articles or miss some?
- False positives: Any non-issues flagged as RED or YELLOW?
- False negatives: Any real issues not caught?
- Backlink resolution: Did broken link detection work correctly?
- Report clarity: Was the output actionable?

**For `ingest-codebase`:**
- File filtering: Were the right files included/excluded?
- CXML quality: Was the output readable and well-ordered?
- Wiki synthesis: Did the article capture architecture accurately?
- Oversized file handling: Were skipped files noted correctly?
- Entry point identification: Were the key files correctly identified?

## Step 4: Identify Improvements

Produce a list of specific, actionable improvements. For each:
- What went wrong (or could go wrong)
- The concrete fix (a new rule, a clarification, an additional step)
- Whether it should go in `program.md` (learned lesson) or `commands/{skill}.md` (structural change to the skill itself)

**`program.md` improvements** (runtime lessons, patterns, heuristics):
- "When ingesting Slack exports, treat messages starting with ':white_check_mark:' as acknowledgments even if they contain nouns"
- "Repos using monorepo structure often have a `packages/` or `apps/` directory — treat each subdirectory as a separate component"

**`commands/{skill}.md` improvements** (structural changes to execution steps):
- "Add a step to check for `.claude/` directory before reading routing table"
- "Move the raw save step before wiki update to avoid partial writes"

Only flag `commands/` changes for issues that can't be handled by a learned heuristic — the skill file is the stable execution path.

## Step 5: Update Program File

Append the lessons to `.claude/programs/{skill}.md`.

If the file doesn't exist, create it with this structure first:

```markdown
# {Skill Name} — Program

This file is the strategy layer for the `{skill}` skill, following the autoresearch `program.md` pattern.
The skill reads this file before executing. Add lessons here — not to the skill file — unless the
execution steps themselves need to change.

## Current Strategy Notes
<!-- High-level guidance that refines how the skill should operate -->

## Known Patterns
<!-- Things that commonly appear and how to handle them correctly -->

## Edge Cases
<!-- Unusual situations encountered and how they were resolved -->

## Do Not
<!-- Anti-patterns: things that seemed right but caused problems -->

## Lessons Log
| Date | Skill Run | Issue Observed | Lesson Added |
|------|-----------|---------------|--------------|
```

Append new lessons to the appropriate section. Add a row to the Lessons Log table.

Never rewrite existing content — append only.

## Step 6: Propose Skill File Edits (if any)

If structural changes to `.claude/commands/{skill}.md` are warranted, show a clear diff-style proposal:

```
PROPOSED CHANGE to .claude/commands/{skill}.md:

CURRENT (Step N):
{current text}

PROPOSED:
{replacement text}

REASON: {why this structural change is needed, not just a learned heuristic}
```

Do NOT apply these changes automatically. Present them for human review.

## Step 7: Report

```
## Skill Improve Report — {skill} — {YYYY-MM-DD}

**Run quality**: [Good / Mixed / Poor]
**Issues found**: N
**Lessons added to program.md**: N
**Proposed skill file changes**: N (or "None")

### Lessons Added
- [brief summary of each lesson]

### Proposed Changes
- [brief summary, or "None"]

### Next Run Guidance
[1-2 sentences on what to watch for in the next execution of this skill]
```

Append an entry to `knowledge-base/log.md`:
```
## [{YYYY-MM-DD}] maintenance | skill-improve: {skill}
Evaluated {skill} run. {N} lessons added to program.md. {N} skill file changes proposed.
```
