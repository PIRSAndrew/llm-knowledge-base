# LLM Knowledge Base

## About
Self-maintaining knowledge base powered by Claude Code. The LLM reads before answering, writes when it learns, and self-improves after every skill execution.

## Key Rules
- Always check `.claude/rules/knowledge-base.md` for the topic routing table before answering domain questions
- Read the wiki article BEFORE answering -- don't answer from memory
- When you learn something new during a session, update the relevant wiki article and add a Key Decisions Log entry
- Raw sources in `raw/` are append-only -- never modify after creation
- Use `[[backlinks]]` between related wiki articles

## Commands
- `/ingest` -- Ingest content from Slack, email, files, or web clips into the knowledge base
- `/kb-lint` -- Run health checks on the knowledge base (broken links, stale content, inconsistencies)
