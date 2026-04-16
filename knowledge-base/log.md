# Knowledge Base Log

Append-only chronological record of ingests, queries, lint passes, and significant wiki updates.
Format: `## [YYYY-MM-DD] action | Subject` -- parseable with `grep "^## \[" log.md | tail -10`

---

## [YYYY-MM-DD] ingest | Initial KB compilation
Compiled first wiki articles from source material. Index created at `_index.md`.

## [2026-04-15] maintenance | Renamed skill-improve → skill-postrun for consistency with PIRS KB
Deleted .claude/commands/skill-improve.md, created skill-postrun.md with identical content. Updated CLAUDE.md and Skill-Wiki Mapping table.

## [2026-04-15] ingest | Codebase: llm-council (PIRSAndrew/llm-council)
Ingested llm-council skill repo from in-session review (repo is private). Created wiki/codebases/llm-council.md, raw/codebases/llm-council-2026-04-15.md, .claude/programs/llm-council.md. Updated karpathy.md, routing table, _index.md, and Skill-Wiki Mapping.

## [2026-04-15] update | Built ingest-codebase and skill-improve commands; added program.md pattern
Created /ingest-codebase (rendergit-style CXML ingestion), /skill-improve (autoresearch feedback loop), and .claude/programs/ strategy files for all three skills. Updated CLAUDE.md and skill-wiki mapping.

## [2026-04-16] maintenance | GitHub daily digest — partial run (MCP scope restricted)
Ran GitHub monitoring agent. Only pirsandrew/llm-knowledge-base was checkable via MCP tools (0 issues, 0 PRs, 0 releases — all clear). Seven repos could not be checked: opendataloader-project/opendataloader-pdf, obra/superpowers, lendtech-http-proxy, banksight, bank-statement-analyzer, PIRSAndrew/opendataloader-pdf fork, PIRSAndrew/superpowers fork. Digest posted to Slack (D0AHAEC9V7W). Root fix needed: expand GitHub MCP server repo scope.

## [2026-04-16] update | llm-council.md — added pending re-ingest note
Added Key Decisions Log entry flagging that raw/codebases/llm-council-2026-04-15.md needs replacement via /ingest-codebase once PIRSAndrew/llm-council goes public.

## [2026-04-15] ingest | Andrej Karpathy GitHub repo assessment
Reviewed all 30 Karpathy repos via WebFetch. Created `wiki/people/karpathy.md` with tiered watch list (4 Tier 1 weekly, 4 Tier 2 monthly, rest archived/skipped). Added to weekly GitHub digest monitoring. Updated routing table and `_index.md`.
