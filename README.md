# LLM Knowledge Base

A self-maintaining knowledge base architecture for [Claude Code](https://docs.anthropic.com/en/docs/claude-code). Your LLM reads before answering, writes when it learns, and self-improves after every task.

Inspired by [Andrej Karpathy's LLM knowledge base pattern](https://x.com/karpathy/status/2039805659525644595): raw sources -> LLM compile -> wiki -> Obsidian vault.

## How It Works

```
  Sources (Slack, Email, Web)
           |
    /ingest (manual) + kb-digest (weekly auto)
           |
           v
     raw/ (append-only archive)
           |
    Filter for decisions
           |
           v
  Routing Table ──> wiki/ (LLM-maintained articles)
                         |          ^
                         v          |
                    Obsidian    KB Feedback
                  (human view)  (skills learn)

  /kb-lint ──> periodic health checks
```

**Three layers:**
1. **raw/** -- Source material preserved verbatim (Slack exports, email digests, web clips, meeting notes). Append-only, never edit after creation.
2. **wiki/** -- Compiled articles organized by domain. The LLM writes and maintains these. Cross-linked with `[[backlinks]]`. Every decision logged with date and source.
3. **Routing table** -- Keyword-to-file mapping in `.claude/rules/knowledge-base.md`. This is how the LLM knows which article to read for any given question. The single most important piece of the system.

## Quick Start

### 1. Clone and open in Claude Code

```bash
git clone https://github.com/<you>/llm-knowledge-base.git my-kb
cd my-kb
claude
```

### 2. Open as Obsidian vault

Open the `knowledge-base/` folder as a vault in [Obsidian](https://obsidian.md). This gives you graph view, backlinks, and a visual browser for the wiki.

### 3. Customize paths

Update the knowledge base path in these files to match your setup:

- `.claude/rules/knowledge-base.md` (line 4)
- `.claude/commands/ingest.md` (line 9)
- `.claude/commands/kb-lint.md` (line 8)

### 4. Write your first articles

Create 3-5 wiki articles on topics you discuss most often. Use the template at `knowledge-base/wiki/example/article-template.md`. Then add them to:

- The routing table in `.claude/rules/knowledge-base.md`
- The index at `knowledge-base/_index.md`

### 5. Start ingesting

```
/ingest #your-slack-channel last 30 days
/ingest inbox last 7 days
/ingest /path/to/meeting-notes.md
```

### 6. Run a health check

```
/kb-lint
```

## What's Included

```
.
├── README.md
├── CLAUDE.md                              # Project instructions for Claude Code
├── knowledge-base/
│   ├── _index.md                          # Master index of all wiki articles
│   ├── wiki/
│   │   ├── example/
│   │   │   └── article-template.md        # Copy this to create new articles
│   │   └── people/
│   │       └── .gitkeep
│   ├── raw/
│   │   ├── slack/                         # Slack channel exports
│   │   ├── emails/                        # Email digests
│   │   ├── meetings/                      # Meeting transcripts
│   │   └── web/                           # Obsidian Web Clipper output
│   ├── web-clip-template.json             # Obsidian Web Clipper template
│   └── x-twitter-template.json            # X/Twitter post clipper template
├── .claude/
│   ├── rules/
│   │   └── knowledge-base.md              # Routing table + read/write behaviors
│   └── commands/
│       ├── ingest.md                      # /ingest slash command
│       └── kb-lint.md                     # /kb-lint health check
└── kb-digest-template.md                  # Weekly digest (copy to scheduled-tasks/)
```

## Web Clipper Setup

Import the two template files into [Obsidian Web Clipper](https://obsidian.md/clipper):

1. `knowledge-base/web-clip-template.json` -- for general web articles
2. `knowledge-base/x-twitter-template.json` -- auto-triggers on `x.com/*/status/*` URLs

Clips land in `raw/web/` with a `routed:` frontmatter property. Unrouted clips get processed on the next `/ingest web clips` or weekly digest run.

## Weekly Digest (Optional Automation)

The `kb-digest-template.md` file is a template for a weekly scheduled task that automatically scans:
- Slack channels for new decisions
- Outlook Focused inbox for decision-relevant emails
- `raw/web/` for unrouted web clips

To set it up, copy it to your Claude Code scheduled tasks directory and customize the channel list:

```bash
mkdir -p ~/.claude/scheduled-tasks/kb-digest
cp kb-digest-template.md ~/.claude/scheduled-tasks/kb-digest/SKILL.md
```

## KB Feedback Block

Add this to the bottom of any Claude Code skill to make it self-improve. After each execution, the skill checks if it learned anything new and updates the wiki:

```markdown
## Knowledge Base Feedback

After completing this task, check if this run revealed new institutional knowledge.

**Check for these signals:**
- New pattern, edge case, or gotcha not previously documented
- Process change or workflow update that affects the wiki
- Data format change, schema discovery, or integration behavior
- New decision or precedent that should be recorded

**If any findings warrant a wiki update:**
1. Read the relevant wiki article
2. Append the finding to the appropriate section
3. Add a Key Decisions Log entry: date, what was learned, source
4. Maintain existing [[backlinks]]

**If nothing new was learned**, skip this step silently.
```

## Design Decisions

| Decision | Why |
|----------|-----|
| **Routing table** | Direct keyword-to-file mapping eliminates guesswork, reduces token waste. The most important piece of the system. |
| **raw/ is append-only** | Audit trail. Trace any wiki error back to its source. |
| **KB Feedback blocks** | Without them, operational learning stays trapped in one session. This makes the system compound over time. |
| **Obsidian** | Free, local-first, `[[wikilinks]]` + graph view native. No sync, no vendor lock-in, no API costs. |
| **Split at ~200 lines** | Keep articles focused. Two distinct topics = two files. |
| **75+ article threshold** | Below 75, the routing table works perfectly. Above, consider vector search or full-text indexing. |

## License

MIT
