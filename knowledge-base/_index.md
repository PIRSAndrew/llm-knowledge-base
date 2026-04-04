# Knowledge Base

## Wiki Articles

### Example
- [[article-template|Example Article]] -- template showing the standard wiki article structure

### People
<!-- - [[person-name|Person Name]] -- role, writing style guide, communication rules -->

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

## How This Knowledge Base Works
- **raw/** -- source material (Slack exports, email threads, meeting notes, web clips) preserved as-is
- **wiki/** -- compiled articles organized by domain, maintained by LLM, cross-linked with backlinks
- Articles include decision logs with dates and sources for traceability
- Open this folder as an Obsidian vault to browse with backlinks and graph view

## Architecture Notes
- At 75+ wiki articles, evaluate adding a dedicated search/index layer (vector embeddings or full-text search) to supplement the routing table. The current topic-to-file routing table works well at small scale but may become a bottleneck as the wiki grows.
