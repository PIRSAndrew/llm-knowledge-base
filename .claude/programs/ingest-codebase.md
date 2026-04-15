# Ingest Codebase — Program

This file is the strategy layer for the `ingest-codebase` skill, following the autoresearch `program.md` pattern.
The skill reads this file before executing. Add lessons here — not to the skill file — unless the
execution steps themselves need to change.

## Current Strategy Notes
- The wiki article goal is synthesis, not transcription. The CXML raw file has the full content — the wiki should answer "how does this work?" not "what is in this repo?".
- Prioritize README, entry-point files, and config files when synthesizing architecture. Tests are secondary.
- When the repo has no README, infer purpose from package metadata (pyproject.toml, package.json) and the main entry point.

## Known Patterns
- **Monorepos**: if a `packages/`, `apps/`, or `services/` directory exists, treat each subdirectory as a separate component in the Architecture section
- **Python projects**: look for `pyproject.toml` or `setup.py` for dependencies, `__main__.py` or CLI entry in `[project.scripts]` for entry points
- **Node projects**: `package.json` `scripts` block reveals entry points; `src/index.ts` or `src/index.js` is usually the root
- **Rust projects**: `src/main.rs` for binaries, `src/lib.rs` for libraries; `Cargo.toml` for dependencies

## Edge Cases
<!-- Unusual situations encountered and how they were resolved -->

## Do Not
- Do not include `.env` files or any file containing credentials in the CXML output — skip and note in the report
- Do not include `*.lock` files (package-lock.json, yarn.lock, poetry.lock, etc.) — they are noise
- Do not write the CXML document into the wiki article — the wiki is the synthesis, raw/ is the CXML

## Lessons Log
| Date | Skill Run | Issue Observed | Lesson Added |
|------|-----------|---------------|--------------|
