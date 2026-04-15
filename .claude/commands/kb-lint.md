---
description: Run health checks on the knowledge base. Finds inconsistencies, broken links, stale content, and pending decisions.
---

# Knowledge Base Lint

Run a health check on the knowledge base at:
`knowledge-base/`

## Step 0: Read Program File

Read `.claude/programs/kb-lint.md` before proceeding. It contains calibration notes, known false-positive patterns, and lessons from past lint runs. Apply those lessons when making severity calls.

## Checks to Run

### 1. Cross-Article Consistency
Read all wiki articles and check for contradictory definitions or conflicting information.
Report any contradictions found across articles.

### 2. Pending / Unresolved Decisions
Grep all wiki articles for: "pending", "TBD", "still valid", "to be confirmed", "awaiting", "open item", "not yet"
Report each finding with the article name, the pending item, and how old it is.

### 3. Backlink Integrity
For every `[[wikilink]]` in every article:
- Check that the referenced file actually exists in the wiki
- Report broken links with the source article and the missing target

### 4. Stale Content
Check each article's most recent Key Decisions Log entry.
Flag articles where the most recent information is older than 3 months.

### 5. Routing Table Completeness
Read `.claude/rules/knowledge-base.md` routing table.
- Check that every wiki article appears in at least one routing table row
- Check that every routing table path resolves to an actual file
- Flag any wiki articles not reachable via the routing table

### 6. Index Completeness
Read `_index.md` and verify:
- Every wiki article file has a corresponding entry in the index
- Every index entry points to a file that exists

### 7. Frontmatter Integrity
Check every wiki article for valid YAML frontmatter:
- Must have `title`, `tags`, `created`, `updated` fields
- `tags` must be an array with at least one tag
- `updated` date should not be older than the most recent Key Decisions Log entry
- Flag articles missing frontmatter or with incomplete fields

### 8. Log Freshness
Read `knowledge-base/log.md`:
- Check the most recent entry date
- Flag if no entries in 7+ days (suggests ingests/updates aren't being logged)
- Check for entries with malformed date prefixes

### 9. Outputs Health
Check `knowledge-base/outputs/`:
- List any filed outputs and verify they have frontmatter (title, tags, created, query)
- Check that filed outputs cross-link to wiki articles via backlinks
- Flag outputs that could be promoted to wiki articles (if they're frequently referenced)

### 10. Skill-Wiki Drift
For each pair in the Skill-Wiki Mapping table:
- Check if the skill file was modified more recently than the wiki article (or vice versa)
- Flag pairs where one side may be out of date

## Output Format

Report findings as a prioritized list:

```
## KB Lint Report -- {date}

### RED (Fix Now)
- [article] -- [issue description]

### YELLOW (Should Fix)
- [article] -- [issue description]

### GREEN (Info Only)
- [article] -- [observation]

### Summary
- Articles scanned: N
- Issues found: N (N red, N yellow, N green)
- Suggested actions: [numbered list]
```
