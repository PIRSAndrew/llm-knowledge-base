---
description: Run health checks on the knowledge base. Finds inconsistencies, broken links, stale content, and pending decisions.
---

# Knowledge Base Lint

Run a health check on the knowledge base at:
`knowledge-base/`

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

### 7. Skill-Wiki Drift
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
