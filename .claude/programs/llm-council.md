# llm-council — Program

This file is the strategy layer for the `llm-council` skill, following the autoresearch `program.md` pattern.
The skill reads this file before executing. Add lessons here — not to the skill file — unless the
execution steps themselves need to change.

## Current Strategy Notes
- The council is most valuable for questions where models are likely to disagree or where one model's blind spot could mislead. Don't invoke for factual lookups where a single model is sufficient.
- Always present the chairman's verdict as the primary answer. Individual responses and rankings are supporting context, not the deliverable.
- When the council produces a KB-worthy synthesis, use `--kb-file` to file it to `knowledge-base/outputs/` rather than letting it go to `/tmp/`.

## Known Patterns
- **Technical architecture questions**: councils consistently surface disagreements between GPT and Gemini on tooling choices; Opus tends to synthesize well
- **Model alias drift**: if a model returns a 404/model-not-found error, the alias table in council.py needs updating — check OpenRouter's model list
- **Parse failures in Stage 2**: when `top = parse failed` appears in stderr, that reviewer's ranking was excluded. If all reviewers fail to parse, Stage 2 aggregate is empty and chairman proceeds with unranked responses — still useful, just note it in the report

## Edge Cases
- **Only 2 models respond**: council proceeds but peer review is thin (each model only sees one other response). Flag this in the presentation.
- **Chairman is same model as a council member**: that model effectively sees its own response twice (once as author, once as chairman). Currently by design — chairman's broader synthesis prompt overrides any anchoring effect.

## Do Not
- Do not invoke the council for quick factual lookups — 9–13 API calls and ~$0.30+ for a question with a known answer is wasteful
- Do not present individual peer review rankings as the verdict — the chairman synthesis is the deliverable
- Do not hard-code model IDs anywhere except MODEL_ALIASES in council.py — always go through the alias table

## Lessons Log
| Date | Skill Run | Issue Observed | Lesson Added |
|------|-----------|---------------|--------------|
