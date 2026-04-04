---
name: kb-digest
description: Weekly Slack + Outlook + web clip digest -- scan monitored sources for new decisions and ingest into the knowledge base
---

You are running the weekly knowledge base digest.

## Knowledge Base Location
`knowledge-base/`

## Monitored Sources

### Slack Channels
Scan these channels for messages from the last 7 days:

| Channel | ID | KB Topics |
|---------|-----|-----------|
| #channel-1 | C00000000 | Topics that flow through this channel |
| #channel-2 | C00000000 | Topics that flow through this channel |
<!-- Add 3-6 channels where decisions happen. -->

### Outlook Inbox
Scan the Focused inbox for the last 7 days. The Focused inbox is already curated -- Outlook's ML filters out newsletters, notifications, and noise.

## Pipeline

### Step 1: Calculate Time Range
Calculate the date 7 days ago from now. Use this as both:
- `oldest` parameter for Slack (Unix timestamp)
- `afterDateTime` parameter for Outlook (ISO date string or natural language like "7 days ago")

### Step 2a: Read Each Slack Channel
For each channel, use `slack_read_channel` with the `oldest` timestamp.
For any messages with threads (indicated by reply count), use `slack_read_thread` for full context.

### Step 2b: Scan Unrouted Web Clips
Scan `knowledge-base/raw/web/` for all `.md` files. Read the frontmatter of each. Process any clip where `routed:` is blank (unrouted):
1. Read the full clip content
2. Determine if the content is relevant to your domain
3. If relevant: identify the target wiki article via the routing table, update the article, set `routed:` to the wiki path
4. If not relevant (general interest, personal reference): set `routed:` to `"n/a -- not domain-specific"` so it's not re-processed
5. Skip clips that already have a `routed` value

### Step 2c: Read Outlook Focused Inbox
Use `outlook_email_search` with:
- `folderName: "Inbox"`
- `afterDateTime: "<7 days ago>"`
- `limit: 50`

For any email that looks decision-relevant based on subject/sender/preview, use `read_resource` with the returned URI to get the full email body.

**Skip these even if they appear in Focused:**
- Calendar invites and meeting confirmations (pure scheduling)
- Read receipts and delivery notifications
- Auto-generated alerts
- Short acknowledgments ("thanks", "got it", "sounds good") with no decision context

### Step 3: Filter for Decisions
Apply the same filter to Slack messages, web clips, and emails.

Skip content that is purely:
- Scheduling ("let's meet tomorrow", "what time works")
- Greetings ("good morning", "thanks")
- Acknowledgments ("ok", "got it", "sounds good" as standalone messages)
- File shares without context

Focus on content that contains:
- Decisions ("yes we should", "confirmed", "let's go with", "approved")
- Requirements ("we need to", "it should", "must have")
- Clarifications (Q&A about how something works or should work)
- Process changes ("from now on", "new rule", "changed to")
- Bug reports or issues that affect existing wiki content

### Step 4: Save Raw
Slack: `knowledge-base/raw/slack/{channel-name}-week-of-{YYYY-MM-DD}.md`
Email: `knowledge-base/raw/emails/inbox-digest-week-of-{YYYY-MM-DD}.md`

Only save content that passed the decision filter. Don't save the entire channel/inbox dump.

### Step 5: Update Wiki
Read the routing table in `.claude/rules/knowledge-base.md` to identify which wiki articles to update.
For each decision/requirement found:
1. Read the relevant wiki article
2. Append the new information to the appropriate section
3. Add an entry to the Key Decisions Log with date and source
   - Slack: who said it, which channel
   - Email: who sent it, subject line
   - Web clip: article title, URL
4. Maintain backlinks

If a decision doesn't map to any existing article, note it in the report.

### Step 6: Report
Produce a summary:

**Slack:**
- Channels scanned: N
- Messages found: N total
- Decisions identified: N (bullet list)

**Web Clips:**
- Unrouted clips found: N
- Routed to wiki: N (list each with clip title and target article)
- Marked n/a: N (not domain-relevant)

**Outlook:**
- Emails scanned: N from Focused inbox
- Decision-relevant emails: N (bullet list with sender + subject + decision summary)

**Combined:**
- Articles updated: which articles and what was added (note source: Slack, email, or web clip)
- Flagged: anything ambiguous or requiring human judgment

If no decision-relevant content was found, report cleanly.
