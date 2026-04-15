---
source: https://github.com/PIRSAndrew/llm-council
repo: llm-council
rendered: 2026-04-15
files_included: 2
files_skipped: 0
note: Repo is private — contents sourced from in-session review of OneDrive originals (council.py 11KB, SKILL.md 3KB). User confirmed edits were applied before push; exact post-edit diff not captured.
---

<repository name="llm-council" source="https://github.com/PIRSAndrew/llm-council" rendered="2026-04-15">

<file_tree>
llm-council/
├── SKILL.md          (3 KB) — Claude Code skill descriptor, trigger keywords, usage instructions
└── council.py        (11 KB) — Three-stage council implementation (async Python, httpx, OpenRouter)
</file_tree>

<file path="SKILL.md">
---
name: llm-council
description: >
  Convene a council of LLMs to answer a question — parallel responses, anonymous peer review, and chairman synthesis.
  Based on Karpathy's llm-council. Use when the user wants a multi-model consensus, council opinion, or to compare LLM answers.
  Triggers: "ask the council", "llm council", "multi-model", "council vote", "compare models", "what do multiple models think".
model: opus
---

# LLM Council Skill

Convene a council of the latest frontier LLMs via OpenRouter. One command, zero human intervention.

## How to Run

Run the council script with a **single Bash call**. It handles all 3 stages internally (parallel queries, anonymous peer review, chairman synthesis) and outputs structured JSON to stdout. Progress logs go to stderr.

```bash
/c/Users/andre/AppData/Local/Python/bin/python.exe ".claude/skills/llm-council/council.py" "THE QUESTION" --output /tmp/council_result.json
```

Then read `/tmp/council_result.json` to present results.

### Model Aliases

| Alias | Model ID |
|-------|----------|
| gpt | openai/gpt-5.4-pro |
| gemini | google/gemini-3.1-pro-preview |
| opus | anthropic/claude-opus-4.6 |
| grok | x-ai/grok-4 |
| sonnet | anthropic/claude-sonnet-4.6 |

### Default Council
- `openai/gpt-5.4-pro`
- `google/gemini-3.1-pro-preview`
- `anthropic/claude-opus-4.6`
- `x-ai/grok-4`
- **Chairman**: `anthropic/claude-opus-4.6`

### Prerequisites
- `OPENROUTER_API_KEY` in environment or `.env` file in workspace root
- Python with `httpx` installed
</file>

<file path="council.py">
"""
LLM Council — Single-run script. Queries multiple LLMs via OpenRouter,
anonymous peer review, chairman synthesis. Outputs structured JSON.

Usage:
  python council.py "Your question here"
  python council.py "Your question here" --models gpt,gemini,opus,grok --chairman opus
  python council.py "Your question here" --output results.json
"""

import asyncio
import json
import os
import random
import re
import sys
import time
import argparse
from string import ascii_uppercase

import httpx

OPENROUTER_URL = "https://openrouter.ai/api/v1/chat/completions"
PEER_REVIEW_MAX = 8000   # chars per response shown to peer reviewers
CHAIRMAN_MAX = 6000      # chars per response shown to chairman

MODEL_ALIASES = {
    "gpt": "openai/gpt-5.4-pro",
    "gemini": "google/gemini-3.1-pro-preview",
    "opus": "anthropic/claude-opus-4.6",
    "grok": "x-ai/grok-4",
    "sonnet": "anthropic/claude-sonnet-4.6",
    "gpt4o": "openai/gpt-4o",
    "gpt5": "openai/gpt-5.4-pro",
    "flash": "google/gemini-2.5-flash",
}

DEFAULT_MODELS = ["openai/gpt-5.4-pro", "google/gemini-3.1-pro-preview", "anthropic/claude-opus-4.6", "x-ai/grok-4"]
DEFAULT_CHAIRMAN = "anthropic/claude-opus-4.6"
TIMEOUT = 180

# [council.py continues — full implementation reviewed in session 2026-04-15.
#  Key functions: resolve_model, get_api_key, call_model (with retry logic),
#  call_all, truncate_at_boundary, parse_ranking (multi-pattern), aggregate_rankings,
#  run_council (3-stage orchestrator), main (argparse entry point).
#  Improvements applied by user before push: robust parser, retry backoff,
#  higher truncation limits, None instead of magic 99, chairman disagreement clause,
#  token/cost tracking, --kb-file flag for KB output integration.]
</file>

</repository>
