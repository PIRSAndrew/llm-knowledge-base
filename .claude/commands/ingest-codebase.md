---
description: Ingest a codebase into the knowledge base. Accepts a local file path or GitHub repo URL. Renders the repo in LLM-optimized CXML format and synthesizes a wiki article on architecture.
argument-hint: [/path/to/repo or https://github.com/owner/repo]
---

# Codebase Ingest

You are ingesting a codebase into the knowledge base at:
`knowledge-base/`

## Step 0: Read Program File

Read `.claude/programs/ingest-codebase.md` before proceeding. It contains lessons from past runs, known edge cases, and patterns that improve output quality. Apply those lessons throughout this run.

## Step 1: Detect Source and Collect File Tree

Parse `$ARGUMENTS` to determine source type.

**Local path** (starts with `/` or `./`, or is a plain directory name):
- Use Glob `**/*` from the repo root to collect all file paths
- Filter out: `.git/`, `node_modules/`, `__pycache__/`, `.venv/`, `venv/`, `dist/`, `build/`, `.next/`, `target/` (Rust), `*.pyc`, `*.class`, `*.o`, `*.so`, `*.dylib`, `*.dll`, `*.exe`, `*.bin`, lock files (`package-lock.json`, `yarn.lock`, `Cargo.lock`, `poetry.lock`, `uv.lock`), image files (`*.png`, `*.jpg`, `*.gif`, `*.ico`, `*.svg`), font files, `.env` and `.env.*` files
- Read each remaining file; skip any file over 500 lines (note it as oversized in the tree)

**GitHub URL** (contains `github.com`):
- Extract owner and repo from the URL
- Use available GitHub MCP tools to fetch the file tree and file contents
- Apply the same filters as above
- Note: if MCP access is restricted to a different repo, report that clearly and stop

**No argument provided:**
- Check if the current working directory looks like a repo (has README, src/, etc.)
- If yes, treat it as a local path ingest
- If no, ask the user to provide a path or URL

## Step 2: Render CXML Document

Produce a flat CXML document representing the entire codebase. This is the LLM-optimized format (inspired by Karpathy's rendergit LLM view):

```
<repository name="{repo-name}" source="{path-or-url}" rendered="{YYYY-MM-DD}">

<file_tree>
{indented file tree showing all included files, marking oversized files with [OVERSIZED - skipped]}
</file_tree>

<file path="{relative/path/to/file}">
{full file contents}
</file>

<file path="{relative/path/to/next/file}">
{full file contents}
</file>

</repository>
```

Order files as: README first, then config files (pyproject.toml, package.json, Makefile, etc.), then source files by directory depth (shallow first), then tests last.

## Step 3: Save Raw

Save the CXML document to:
`knowledge-base/raw/codebases/{repo-name}-{YYYY-MM-DD}.md`

Wrap with a source header:
```markdown
---
source: {path-or-url}
repo: {repo-name}
rendered: {YYYY-MM-DD}
files_included: N
files_skipped: N (list reasons: oversized, binary, filtered-dir)
---

{CXML document}
```

Raw saves are append-only — never modify after creation.

## Step 4: Synthesize Wiki Article

Read the CXML document and produce a structured wiki article. Synthesize — do not dump raw content into the wiki.

Save to `knowledge-base/wiki/codebases/{repo-name}.md` (create `wiki/codebases/` if it doesn't exist).

Article structure:
```markdown
---
title: "{Repo Name} — Codebase"
tags:
  - codebase
  - {language}
  - {domain-tag}
created: {YYYY-MM-DD}
updated: {YYYY-MM-DD}
---

# {Repo Name}

## Overview
What this codebase does. 2-3 sentences max. Include the GitHub URL if ingested from GitHub.

## Architecture
How the system is structured. Key directories, their roles, data flow between components.
Use a simple diagram in text if helpful (boxes and arrows).

## Entry Points
The main files to read first when understanding this codebase:
- `path/to/file.py` — what it does
- `path/to/another.py` — what it does

## Key Components
One subsection per major component or module. For each:
- What it does
- Key functions/classes
- Dependencies on other components

## Dependencies
External libraries and services this codebase depends on. Note anything unusual or version-pinned.

## Configuration
How it's configured (env vars, config files, flags). What must be set to run it.

## How to Run
Steps to run it locally, if discernible from README or Makefile.

## Notes & Gotchas
Anything non-obvious: unusual patterns, known issues, technical debt, things to watch out for.

## Related
<!-- [[other-wiki-article|Display Name]] -->

## Key Decisions Log
| Date | Decision | Source |
|------|----------|--------|
| {YYYY-MM-DD} | Initial codebase ingested from {source} | ingest-codebase run |
```

## Step 5: Update Index and Routing Table

If `wiki/codebases/` is a new subdirectory:
- Add a `### Codebases` section to `knowledge-base/_index.md`

Always:
- Add a line under `### Codebases` in `_index.md`: `- [[{repo-name}|{Repo Name}]] -- {one-line description}`
- Add a row to the routing table in `.claude/rules/knowledge-base.md`:
  `| **{repo-name} codebase / {keywords}** | \`wiki/codebases/{repo-name}.md\` | (none) |`

## Step 6: Append to Log

```
## [{YYYY-MM-DD}] ingest | Codebase: {repo-name}
Ingested {N} files from {source}. {N} files skipped (oversized/binary/filtered). Wiki article created at wiki/codebases/{repo-name}.md.
```

## Step 7: Report

- **Source**: path or URL ingested
- **Files included**: N (list any notable oversized/skipped files)
- **Raw saved to**: `knowledge-base/raw/codebases/{repo-name}-{YYYY-MM-DD}.md`
- **Wiki article**: `knowledge-base/wiki/codebases/{repo-name}.md`
- **Key findings**: 3-5 bullet points on the most important architectural facts
- **Flagged**: anything unclear, missing docs, or worth a follow-up ingest
