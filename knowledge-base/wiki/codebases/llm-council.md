---
title: "llm-council — Codebase"
tags:
  - codebase
  - llm
  - multi-model
  - openrouter
  - python
created: 2026-04-15
updated: 2026-04-15
---

# llm-council

## Overview
A Claude Code skill that convenes a council of frontier LLMs via OpenRouter to answer a question. Implements Karpathy's three-stage council pattern: parallel independent responses → anonymous peer review → chairman synthesis. Lives at `https://github.com/PIRSAndrew/llm-council`. Invoked automatically by Claude Code when the user says "ask the council", "multi-model", "council vote", etc.

## Architecture

```
User query
    │
    ▼
Stage 1: Parallel collection
    │  call_all() → asyncio.gather → N simultaneous OpenRouter calls
    │  Each model answers independently
    ▼
Stage 2: Anonymous peer review
    │  Responses shuffled + relabeled (A/B/C/D) to remove identity
    │  All N models review and rank the labeled responses
    │  parse_ranking() extracts order → aggregate_rankings() averages positions
    ▼
Stage 3: Chairman synthesis
    │  Chairman sees: full responses (deanonymized) + aggregate ranking
    │  Produces authoritative answer combining strongest elements
    ▼
JSON output (stdout) + progress logs (stderr)
```

## Entry Points
- `council.py` — the entire implementation; run directly via Python
- `SKILL.md` — Claude Code skill descriptor; controls when and how Claude invokes council.py

## Key Components

### `call_model` / `call_all`
Async HTTP to OpenRouter's `/api/v1/chat/completions`. `call_all` runs all models in parallel via `asyncio.gather`. Includes retry logic with exponential backoff (1s, 2s) on timeouts and 5xx/429 errors.

### `parse_ranking`
Extracts ranked order from peer review text. Uses multiple regex patterns to handle format variation across models (bare labels, bold, dashes). Falls back gracefully — parse failures return `None` and are excluded from aggregation rather than assigned a magic penalty value.

### `aggregate_rankings`
Averages position scores across all peer reviewers. Lower average = ranked higher. Models with no valid rankings appear in output with `average_rank: null` rather than being silently penalized.

### `run_council`
Orchestrates the three stages end-to-end. Minimum 2 successful Stage 1 responses required to proceed. Stage 2 uses `truncate_at_boundary()` (8000 chars, paragraph boundary) for peer review; Stage 3 uses 6000 chars per response for chairman.

### Chairman prompt
Instructs the chairman to combine strongest elements, correct errors, and — critically — *surface factual disagreements rather than paper over them*.

## Dependencies
- `httpx` — async HTTP client
- `asyncio` — Python stdlib concurrency
- OpenRouter API (`OPENROUTER_API_KEY` required)
- No other dependencies

## Configuration
- `OPENROUTER_API_KEY` — env var or `.env` file in workspace root or grandparent directory
- `--models` — comma-separated aliases or full model IDs
- `--chairman` — which model synthesizes (default: Claude Opus 4.6)
- `--output` — save full JSON to file
- `--kb-file` — also write a markdown synthesis to `knowledge-base/outputs/` with frontmatter

## How to Run
```bash
# Basic
python council.py "Your question here"

# Custom models + output file
python council.py "Question" --models gpt,gemini,opus,grok --chairman opus --output /tmp/result.json

# With KB integration
python council.py "Question" --kb-file knowledge-base/outputs/council-{slug}.md
```

## Notes & Gotchas
- Makes 9–13 API calls per run across premium models — note cost (~$0.10–$0.50+ depending on question length and models)
- Python path in SKILL.md is hardcoded to Windows (`/c/Users/andre/AppData/Local/Python/bin/python.exe`) — needs updating on other machines
- Model aliases (`gpt`, `gemini`, etc.) point to specific versioned model IDs that drift as providers release new models — check the alias table periodically
- The ranking parser handles format variation but is not infallible; `parse failed` in Stage 2 output means a reviewer's ranking was excluded from aggregation
- Label-to-model mapping is randomized each run — prevents position bias but means label assignments differ between runs

## Related
- [[karpathy|Andrej Karpathy — GitHub Monitoring]] — original llm-council pattern this is based on
- [[article-template|KB Article Template]]

## Key Decisions Log
| Date | Decision | Source |
|------|----------|--------|
| 2026-04-15 | Repo created at PIRSAndrew/llm-council. Implementation adapted from Karpathy's llm-council web app into a single-script CLI optimized for Claude Code skill invocation. | Session review |
| 2026-04-15 | Applied fixes before push: robust multi-pattern ranking parser, retry backoff, truncation raised to 8000/6000 chars at paragraph boundary, None instead of magic-99 for parse failures, chairman disagreement clause, token tracking, --kb-file flag. | In-session code review |
