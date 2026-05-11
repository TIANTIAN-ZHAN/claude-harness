# CLAUDE.md — claude-harness

This repo is itself a Claude Code skill toolkit. Everything here is documentation + SKILL.md files. **No application code.**

## What this repo is

A toolkit for the **post-vibe-coding era**: skills that scaffold and maintain an AI agent harness in any project. The README is the story; `skills/*/SKILL.md` are the actual deliverables.

## Project Structure

```
claude-harness/
├── README.md              # story + install + skill index (the public face)
├── LICENSE                # MIT
├── CLAUDE.md              # this file
├── .claude-plugin/        # Claude Code plugin manifest
│   └── plugin.json
├── skills/                # ← the actual product
│   ├── bootstrap-harness/SKILL.md
│   └── review-harness/SKILL.md
└── docs/                  # long-form supporting material
    ├── narrative.md       # the "From vibe to harness" essay
    ├── research.md        # cited research with links
    └── roadmap.md         # upcoming skills
```

## Conventions

- **All public-facing files in English.** Skills SKILL.md may keep Chinese templates internally where the audience is bilingual, but README / docs / commit messages are English-first.
- **SKILL.md ≤ 500 lines.** Front-load the *when/why*; push templates and anti-patterns to the bottom or to `reference/` subdirs.
- **No emoji decoration** in headings unless it carries semantic weight (✅ / ⚠️ / 🚫 for boundary tiers is OK).
- **Cite research with links** when making claims. No naked "studies show".

## Git Workflow

- Commit messages in English, imperative mood ("Add finish-phase skill", not "Added")
- No `git push --force`, no `--no-verify`
- Tag releases as `v0.1.0`, `v0.2.0`...

## Boundaries

### ✅ Always
- Add new skills under `skills/<name>/SKILL.md`
- Edit `docs/` long-form essays
- Update `README.md` skill table when adding/removing a skill
- Update `.claude-plugin/plugin.json` skill list in lockstep with `skills/`

### ⚠️ Ask first
- Renaming an existing skill (breaks installed users)
- Removing a skill from `plugin.json` (same)
- Changing the project name or repo URL (breaks marketplace add command)
- Adding a non-MIT dependency

### 🚫 Never
- Write application code in this repo — it's a docs/skill toolkit, not a runtime
- Auto-edit `SKILL.md` from within `/review-harness` (the skill itself forbids file edits during review)
- Push to GitHub without explicit user instruction
- Commit secrets / API keys / personal paths

## Defaults when ambiguous

- **Story over feature** — every skill addition needs a one-line "what vibe-coding pain does this fix?"
- **Cite over assert** — if a claim isn't backed by a public link, soften it
- **Less skills > more skills** — Karpathy got 124k stars with one file. Don't pad the toolkit.
- **English-first** — international reach is the whole point of open sourcing

## harness index

| What you need | Where |
|---|---|
| Public-facing pitch | `README.md` |
| Long-form story | `docs/narrative.md` |
| Research citations | `docs/research.md` |
| Upcoming skills | `docs/roadmap.md` |
| Actual skills | `skills/<name>/SKILL.md` |
