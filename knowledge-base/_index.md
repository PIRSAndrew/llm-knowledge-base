# Knowledge Base

## Wiki Articles

### Example
- [[article-template|Example Article]] -- template showing the standard wiki article structure

### People
- [[karpathy|Andrej Karpathy]] -- GitHub monitoring, repo assessment, weekly watch list

## Key Contacts
| Name | Role | Notes |
|------|------|-------|
<!-- | Name | Role | Context for the LLM | -->

## Source Channels
<!-- - **#channel-name** (channel-id) -- what topics flow through this channel -->

## Web Clips
- **raw/web/** -- articles, X posts, and web content clipped via Obsidian Web Clipper
- Clips have frontmatter: title, url, domain, author, published, clipped date, tags, routed (wiki target)
- Unrouted clips (`routed:` is blank) are pending ingestion into wiki articles

## Activity Log
- **log.md** -- Append-only record of ingests, updates, lint passes, and filed outputs. Check `log.md` for recent activity.

## Filed Outputs
- **outputs/** -- Substantive answers that synthesize multiple wiki articles. Each has frontmatter and cross-links to source articles.

## How This Knowledge Base Works
- **raw/** -- source material (Slack exports, email threads, meeting transcripts, web clips) preserved as-is
- **wiki/** -- compiled articles organized by domain, maintained by LLM, cross-linked with backlinks
- **log.md** -- chronological activity record (ingests, updates, maintenance)
- **outputs/** -- filed answers worth preserving (multi-article syntheses, analyses)
- Articles include decision logs with dates and sources for traceability
- All articles have YAML frontmatter (title, tags, created, updated) for Obsidian Dataview
- Open this folder as an Obsidian vault to browse with backlinks and graph view

## Architecture Notes
- At 75+ wiki articles, evaluate adding a dedicated search/index layer (vector embeddings or full-text search) to supplement the routing table. The current topic-to-file routing table works well at small scale but may become a bottleneck as the wiki grows.
