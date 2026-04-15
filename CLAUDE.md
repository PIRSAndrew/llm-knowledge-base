# LLM Knowledge Base

## About
Self-maintaining knowledge base powered by Claude Code. The LLM reads before answering, writes when it learns, and self-improves after every skill execution.

## Key Rules
- Always check `.claude/rules/knowledge-base.md` for the topic routing table before answering domain questions
- Read the wiki article BEFORE answering -- don't answer from memory
- When you learn something new during a session, update the relevant wiki article and add a Key Decisions Log entry
- Raw sources in `raw/` are append-only -- never modify after creation
- Use `[[backlinks]]` between related wiki articles
- All wiki articles must have YAML frontmatter (title, tags, created, updated)
- Log every ingest, update, and lint pass to `knowledge-base/log.md`
- File substantive multi-article syntheses to `knowledge-base/outputs/`

## Commands
- `/ingest` -- Ingest content from Slack, email, files, or web clips into the knowledge base
- `/ingest-codebase` -- Ingest a codebase (local path or GitHub URL) as CXML flat doc + wiki architecture article
- `/kb-lint` -- Run health checks on the knowledge base (broken links, stale content, inconsistencies, frontmatter, log freshness)
- `/skill-improve` -- Evaluate a recent skill run and update its program.md with lessons (autoresearch feedback loop)

## Skill Program Files
Each skill has a companion strategy file in `.claude/programs/` that accumulates lessons from past runs.
Skills read their program file before executing. Run `/skill-improve` after any skill to close the loop.
