# Knowledge Base Log

Append-only chronological record of ingests, queries, lint passes, and significant wiki updates.
Format: `## [YYYY-MM-DD] action | Subject` -- parseable with `grep "^## \[" log.md | tail -10`

---

## [YYYY-MM-DD] ingest | Initial KB compilation
Compiled first wiki articles from source material. Index created at `_index.md`.

## [2026-04-15] ingest | Andrej Karpathy GitHub repo assessment
Reviewed all 30 Karpathy repos via WebFetch. Created `wiki/people/karpathy.md` with tiered watch list (4 Tier 1 weekly, 4 Tier 2 monthly, rest archived/skipped). Added to weekly GitHub digest monitoring. Updated routing table and `_index.md`.
