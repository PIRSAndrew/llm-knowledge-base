---
title: "Andrej Karpathy — GitHub Monitoring"
tags:
  - people
  - monitoring
  - llm
  - research
created: 2026-04-15
updated: 2026-04-15
---

# Andrej Karpathy — GitHub Monitoring

## Overview
Andrej Karpathy ([@karpathy](https://github.com/karpathy)) is the intellectual origin of this knowledge base architecture — his "Karpathy-style" LLM-as-compiler pattern (raw sources → LLM compile → structured wiki) directly inspired the design of this system. He is a former OpenAI/Tesla AI lead and Stanford researcher (168k GitHub followers) whose active projects frequently surface patterns directly applicable to LLM-powered tooling.

We monitor his GitHub weekly as part of the GitHub digest. This article tracks which repos matter, why, and what to watch for.

## Repo Assessment

### Tier 1 — Watch Weekly (active + highly relevant)

| Repo | Stars | Last Updated | Why It Matters |
|------|-------|-------------|----------------|
| [nanochat](https://github.com/karpathy/nanochat) | 51.9k | Apr 14, 2026 | Complete LLM pipeline (pretrain → SFT → RL → inference) for ~$100 on a single GPU. Architecture patterns directly applicable to any KB-backed chat system. Currently the most actively maintained repo. |
| [autoresearch](https://github.com/karpathy/autoresearch) | 72.7k | Mar 26, 2026 | AI agents autonomously run ML experiments — agents edit `train.py`, run for 5min, evaluate, iterate. The `program.md` instruction pattern is directly analogous to how we structure agent skills. Core read. |
| [karpathy.github.io](https://github.com/karpathy/karpathy.github.io) | 1.2k | Apr 10, 2026 | His blog. Essays here often signal his next major project or a mental model shift worth capturing. Check for new posts each week. |
| [llm-council](https://github.com/karpathy/llm-council) | 17.1k | Nov 22, 2025 | Multi-LLM council: parallel queries → peer review (anonymous) → chairman synthesis. The three-stage pattern is worth adopting for high-stakes KB queries. |

### Tier 2 — Watch Monthly (useful tooling, slower cadence)

| Repo | Stars | Last Updated | Why It Matters |
|------|-------|-------------|----------------|
| [reader3](https://github.com/karpathy/reader3) | 3.5k | Nov 18, 2025 | LLM-integrated book reading. Patterns for chunking long documents into KB articles. |
| [rendergit](https://github.com/karpathy/rendergit) | 2.2k | Aug 21, 2025 | Renders git repos as static HTML for LLM consumption. Useful if we ever need to ingest a codebase as a KB source. |
| [hn-time-capsule](https://github.com/karpathy/hn-time-capsule) | 603 | Dec 10, 2025 | LLMs analyzing Hacker News threads over time. Relevant for web-clip ingestion patterns. |
| [rustbpe](https://github.com/karpathy/rustbpe) | 430 | Jan 3, 2026 | Rust BPE tokenizer training ("the missing tiktoken training code"). Low priority but worth noting if tokenization ever becomes relevant. |

### Tier 3 — No Regular Monitoring (foundational/archived, stable)

These repos are important historically but unlikely to update meaningfully. Read them for education, not monitoring.

| Repo | Stars | Notes |
|------|-------|-------|
| [nanoGPT](https://github.com/karpathy/nanoGPT) | 56.7k | Foundational GPT training reference. Largely superseded by `nanochat`. |
| [llm.c](https://github.com/karpathy/llm.c) | 29.6k | LLM training in pure C/CUDA. Excellent for understanding compute fundamentals. |
| [nn-zero-to-hero](https://github.com/karpathy/nn-zero-to-hero) | 21.4k | Neural net education video series. Stable. |
| [minGPT](https://github.com/karpathy/minGPT) | 24.2k | Minimal GPT PyTorch impl. Superseded by nanoGPT. |
| [micrograd](https://github.com/karpathy/micrograd) | 15.5k | Tiny autograd engine. Stable reference. |
| [llama2.c](https://github.com/karpathy/llama2.c) | 19.4k | Llama 2 inference in C. Archived. |
| [minbpe](https://github.com/karpathy/minbpe) | 10.4k | Clean BPE implementation. Stable. |
| [LLM101n](https://github.com/karpathy/LLM101n) | 36.8k | Archived course. |
| [arxiv-sanity-lite](https://github.com/karpathy/arxiv-sanity-lite) | 1.6k | Paper tagging/recommendation. Stable. |

### Skip Entirely (not relevant to this KB's domain)

- `cpython` (fork, just tracking CPython)
- `jobs` (BLS data visualization, personal project)
- `calorie`, `notpygamejs`, `randomfun` (personal/experimental tools)
- `convnetjs`, `char-rnn` (archived JS/Lua deep learning, pre-transformer era)
- `lecun1989-repro` (historical reproduction, academic)

## Weekly Monitoring Checklist

When running the weekly GitHub digest, check these for Karpathy:

1. **New commits on `nanochat`** — any architecture changes, new training recipes, or speedrun records
2. **New commits on `autoresearch`** — changes to `program.md` instruction patterns or agent loop
3. **New blog posts on `karpathy.github.io`** — fetch `https://karpathy.github.io` and check for new entries
4. **New repos** — fetch `https://github.com/karpathy?tab=repositories` and compare against this list
5. **Star velocity on Tier 1 repos** — rapid star growth often precedes a blog post or HN feature worth ingesting

## Key Projects — Deeper Notes

### nanochat
- Single complexity knob: `--depth` (transformer layers) → all other hyperparameters derived automatically
- Full pipeline: BPE tokenizer → pretrain → SFT → RL → KV-cache inference → web chat UI
- Maintains a "GPT-2 Speedrun" leaderboard (current best: 1.65 hours as of Mar 2026)
- Powered by `autoresearch` agents for iterative improvement
- ~$100 compute budget on a single GPU node

### autoresearch
- Agents edit `train.py`, train for exactly 5 minutes, measure `val_bpb` (bits-per-byte, vocab-size-independent)
- Human role: write `program.md` instructions, not Python code
- ~100 experiments per overnight run
- Directly analogous to our skill→wiki feedback loop

### llm-council
- Stage 1: All council members answer independently
- Stage 2: Each member reviews others' answers (identities anonymized to prevent bias), ranks by accuracy/insight
- Stage 3: A designated "Chairman" model synthesizes into a final authoritative answer
- Uses OpenRouter API + FastAPI backend + React frontend

## Related
- [[article-template|KB Article Template]]

## Key Decisions Log
| Date | Decision | Source |
|------|----------|--------|
| 2026-04-15 | Added Karpathy to weekly GitHub digest monitoring. Tier 1: nanochat, autoresearch, blog, llm-council. | User instruction, GitHub digest session |
| 2026-04-15 | Initial repo assessment: 30 repos reviewed, 4 added to Tier 1 weekly watch, 4 to Tier 2 monthly, rest archived or skipped. | WebFetch of github.com/karpathy |
