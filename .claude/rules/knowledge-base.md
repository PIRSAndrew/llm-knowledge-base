# Knowledge Base Rules

This project maintains an LLM-compiled knowledge base at:
`knowledge-base/`

## Topic Routing Table

Before answering domain questions, read the relevant wiki article(s). Match the question topic to the articles below. Read the most specific match first, then supporting articles if needed. Do NOT read the full index -- go directly to the article path.

| Topic | Primary Article | Supporting Articles |
|-------|----------------|-------------------|
| **Example topic** | `wiki/example/article-template.md` | (none) |
| **Karpathy repos / GitHub monitoring / nanochat / autoresearch / llm-council** | `wiki/people/karpathy.md` | `wiki/codebases/llm-council.md` |
| **llm-council codebase / council.py / OpenRouter / multi-model / peer review** | `wiki/codebases/llm-council.md` | `wiki/people/karpathy.md` |
<!-- Add one row per topic. Be specific with keywords so the LLM matches accurately. -->
<!-- Include catch-all rows for ambiguous terms that could match multiple articles. -->

If a question doesn't match any topic above, read `knowledge-base/_index.md` to find the right article.

## Reading Behavior
- Read the article BEFORE answering -- don't answer from memory and then check
- If a question spans multiple topics, read both the primary and supporting articles
- Cite which article your answer draws from so the user knows the source
- If the wiki doesn't cover the topic, say so -- don't fabricate from general knowledge

## Writing Behavior (active -- do this automatically)
When you encounter a new decision, clarification, or requirement during a session (from Slack, email, meetings, or conversation):
1. Append the raw source to the appropriate file in `raw/` (or create a new one)
2. Update the relevant wiki article with the new information
3. Add an entry to the article's Key Decisions Log with date and source
4. Update `_index.md` if a new article was created
5. If a new topic emerges that doesn't fit existing articles, create a new one under the appropriate `wiki/` subdirectory

Preserve existing content -- append or modify, don't rewrite from scratch. Maintain `[[backlinks]]` between related articles using Obsidian wiki-link syntax. Every decision logged must include: date, what was decided, and who decided it.

## Conventions
- Wiki articles are owned by the LLM -- the user views them in Obsidian but doesn't edit directly
- Raw sources are append-only -- never modify raw exports after creation
- Use Obsidian wiki-link syntax: `[[filename|Display Text]]` for cross-references
- Decision logs use markdown tables with Date, Decision, Source columns
- New projects get their own subdirectory under `wiki/` and an entry in `_index.md`
- Keep articles focused -- one topic per file, split if an article grows beyond a single concern

## Known Inconsistencies (flag these if relevant to the question)
<!-- Track contradictions across articles here. When resolved, move to Resolved. -->
- (none yet)

## Resolved Inconsistencies
<!-- Record what was resolved, when, and the canonical answer. -->
- (none yet)

## Skill-Wiki Mapping

Skills (`.claude/skills/`) contain execution instructions. Wiki articles contain institutional knowledge. They must stay in sync. When a skill is updated during a session (new pattern, gotcha, precedent, or workflow change), update the corresponding wiki article's methodology section and add a Key Decisions Log entry. When a new decision lands in the wiki that affects operational procedure, flag it for skill update.

| Skill | Program File | Wiki Article | Sync Priority |
|-------|-------------|--------------|---------------|
| `ingest` | `.claude/programs/ingest.md` | (none yet) | MEDIUM |
| `kb-lint` | `.claude/programs/kb-lint.md` | (none yet) | LOW |
| `ingest-codebase` | `.claude/programs/ingest-codebase.md` | `wiki/codebases/{repo}.md` per run | MEDIUM |
| `skill-postrun` | (self-referential — reads target skill's program) | (none) | LOW |
| `llm-council` | `.claude/programs/llm-council.md` | `wiki/codebases/llm-council.md` | MEDIUM |
<!-- HIGH = updated frequently, MEDIUM = updated occasionally, LOW = rarely changes -->

## Logging Behavior (active -- do this automatically)
After every ingest, significant wiki update, lint pass, or filed output, append an entry to `knowledge-base/log.md`:
- Format: `## [YYYY-MM-DD] action | Subject` followed by a 1-2 sentence description
- Actions: `ingest`, `query-filed`, `lint`, `maintenance`, `update`
- Keep entries concise and parseable -- one entry per operation
- The log is append-only -- never rewrite or reorder existing entries

## Answer Filing (active -- do this when appropriate)
When a query produces a substantive synthesis worth preserving (comparisons, analyses, decision frameworks, multi-article answers):
1. Write the answer as a markdown file in `knowledge-base/outputs/` with YAML frontmatter (title, tags, created, query)
2. Cross-link to relevant wiki articles using `[[backlinks]]`
3. Append an entry to `log.md` with action `query-filed`
4. If the output is significant enough, consider promoting it to a wiki article under `wiki/`

Not every answer needs filing -- only those that synthesize multiple sources or produce reusable knowledge. Quick lookups and single-article answers stay in chat.

## Frontmatter Conventions
All wiki articles use YAML frontmatter for Obsidian Dataview compatibility:
```yaml
---
title: "Article Title"
tags:
  - domain-tag
  - topic-tag
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```
- Update the `updated` field whenever the article is modified
- Tags should be lowercase, hyphenated, and drawn from a consistent vocabulary
- New articles must include frontmatter from creation

## Linting (periodic, when asked or when it makes sense)
- Check for inconsistencies across articles (e.g., contradictory decisions)
- Flag decisions that are marked pending/unresolved
- Suggest new articles for topics referenced but not yet documented
- Verify backlinks resolve to actual files
- Check skill-wiki pairs for drift (skill updated more recently than wiki, or vice versa)
- Verify all wiki articles have valid YAML frontmatter
- Check log.md for recent activity -- flag if no entries in 7+ days
