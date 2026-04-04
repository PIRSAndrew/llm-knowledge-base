# Knowledge Base Rules

This project maintains an LLM-compiled knowledge base at:
`knowledge-base/`

## Topic Routing Table

Before answering domain questions, read the relevant wiki article(s). Match the question topic to the articles below. Read the most specific match first, then supporting articles if needed. Do NOT read the full index -- go directly to the article path.

| Topic | Primary Article | Supporting Articles |
|-------|----------------|-------------------|
| **Example topic** | `wiki/example/article-template.md` | (none) |
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

| Skill | Wiki Article | Sync Priority |
|-------|-------------|---------------|
<!-- Add one row per skill that has a corresponding wiki article. -->
<!-- HIGH = updated frequently, MEDIUM = updated occasionally, LOW = rarely changes -->

## Linting (periodic, when asked or when it makes sense)
- Check for inconsistencies across articles (e.g., contradictory decisions)
- Flag decisions that are marked pending/unresolved
- Suggest new articles for topics referenced but not yet documented
- Verify backlinks resolve to actual files
- Check skill-wiki pairs for drift (skill updated more recently than wiki, or vice versa)
