---
description: Ingest a source into the knowledge base. Accepts Slack URLs, file paths, channel names with time ranges, Outlook email searches, or web clips.
argument-hint: [slack-url, file-path, #channel last N days, inbox last N days, or web clips]
---

# Knowledge Base Ingest

You are ingesting new content into the knowledge base at:
`knowledge-base/`

## Step 1: Detect Source Type and Fetch Content

Parse `$ARGUMENTS` to determine the source:

**Slack permalink** (contains `slack.com/archives/`):
- Extract channel ID and message timestamp from the URL
- Use `slack_read_thread` if it's a threaded message, otherwise `slack_read_channel` with that timestamp range
- Also read surrounding context (2-3 messages before/after) for full picture

**File path** (contains `\` or `/` with a file extension):
- For `.md` files: use Read tool directly
- For `.docx` files: extract text using python zipfile/xml approach
- For `.xlsx` files: extract using python openpyxl
- For `.pdf` files: use Read tool (supports PDFs)

**Channel + time range** (starts with `#` followed by words like "last 7 days", "this week", "since Monday"):
- Search for the channel using `slack_search_channels`
- Calculate the `oldest` unix timestamp from the time range
- Use `slack_read_channel` with the oldest parameter
- For any messages with threads, also read the full threads

**Outlook email search** (contains "inbox", "email", or "outlook" followed by a search term or date range):
- Use `outlook_email_search` with appropriate filters:
  - Date range: `afterDateTime` / `beforeDateTime`
  - Sender: `sender` parameter
  - Topic: `query` parameter
  - Always use `folderName: "Inbox"` to stay within the main inbox
- Note: `folderName` and `query` cannot be used together. If both are needed, use folder + date filters, then filter results manually.
- For each matching email, use `read_resource` with the returned URI to get full body
- Filter for decision-relevant content (skip scheduling, acknowledgments, notifications)

**Web clips** (argument is "web clips", "unrouted clips", or "raw/web"):
- Scan `knowledge-base/raw/web/` for all .md files
- Read frontmatter of each clip -- check the `routed` property
- Process unrouted clips (where `routed` is blank): read content, identify wiki target via routing table
- For each clip, update the relevant wiki article and set the `routed` property to the wiki article path
- Skip clips that already have a `routed` value (already processed)

## Step 2: Save Raw Content

Save the raw content to the appropriate subdirectory:
- Slack content: `knowledge-base/raw/slack/{source-description}-{YYYY-MM-DD}.md`
- Meeting transcripts: `knowledge-base/raw/meetings/{source-description}-{YYYY-MM-DD}.md`
- Email content: `knowledge-base/raw/emails/{source-description}-{YYYY-MM-DD}.md`
- Web clips: already in `knowledge-base/raw/web/` (no copy needed)
- Other files: `knowledge-base/raw/{source-description}-{YYYY-MM-DD}.md`

Format raw saves with:
- Source header (channel name, file path, URL)
- Date of content / date range
- Full content verbatim (do not summarize raw saves)

## Step 3: Identify Relevant Wiki Articles

Read the routing table in `.claude/rules/knowledge-base.md` to map the content to existing wiki articles. Look for:
- Topic keywords matching the routing table
- People/channels that typically discuss certain topics
- Explicit references to existing wiki concepts

If the content introduces a genuinely new topic not covered by any article, plan to create a new one.

## Step 4: Update Wiki Articles

For each relevant wiki article:
1. Read the current article
2. Identify what's new (decisions, clarifications, requirements, process changes)
3. Skip content that's purely scheduling, greetings, or acknowledgments
4. Append new information to the appropriate section of the article
5. Add entries to the Key Decisions Log table with: date, what was decided, who decided it
6. Maintain existing `[[backlinks]]` and add new ones if the content connects to other articles

If creating a new article:
- Follow the existing article structure (Overview, sections, Related links, Key Decisions Log)
- Save under the appropriate `wiki/` subdirectory
- Add entry to `knowledge-base/_index.md`
- Add entry to the routing table in `.claude/rules/knowledge-base.md`

## Step 5: Report

After ingestion, report:
- **Source**: What was ingested (channel, file, URL)
- **Raw saved to**: File path of the raw save
- **Decisions found**: Bullet list of decisions/requirements identified
- **Articles updated**: Which wiki articles were modified and what was added
- **New articles**: Any new articles created
- **Skipped**: Content that was not decision-relevant (brief note)
- **Flagged**: Anything ambiguous or needing clarification
