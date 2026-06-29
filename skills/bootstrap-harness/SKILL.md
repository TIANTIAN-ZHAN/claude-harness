---
name: bootstrap-harness
description: Create a complete AI-agent harness (CLAUDE.md + supporting docs system) for any project. Portable across Python/Java/TS/Go. Based on 2025-2026 best practices (AGENTS.md spec, GitHub's 2,500-repo study, ACE paper, context-engineering research). Use when a repo has no harness, was just cloned/scaffolded, or its docs are bloated/stale and need a structured rebuild.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, AskUserQuestion, TodoWrite
---

# bootstrap-harness

Create a production-quality harness (docs system for AI agents) in any repo. **3 progressive stages** — produce only what the project needs at its current maturity.

File templates live in [templates.md](templates.md) — read it when you reach Step 3, not before.

## When to use

- **Fresh repo / no CLAUDE.md** — just cloned or scaffolded, wants AI-agent workflow
- **Repo with no harness** — team adopting structured AI coding
- **Major rebuild** — current docs bloated / stale / drifted

## When NOT to use

- **Minor tweaks / drift** → `/review-harness` (incremental, no rebuild)
- **Single-file script / experiment** → overkill
- **Harness exists and mostly accurate** → `/review-harness` first to confirm

---

## Core principles (2025-2026 research)

1. **Commands go first** — agents query build/test/run most often (GitHub 2,500-repo study).
2. **✅ / ⚠️ / 🚫 three-tier Boundaries** — Always / Ask first / Never (AGENTS.md spec).
3. **CORRECT / INCORRECT code pairs** — concrete examples beat prose; pull from real repo code.
4. **CLAUDE.md hard limit < 60 lines.** SOTA: instruction-following degrades past ~150-200 total instructions, and — worse — a bloated file gets judged "irrelevant" and **ignored wholesale**, taking your good rules down with it. Design CLAUDE.md as a **pointer, not a description**. Test every line: *"would removing this cause the agent to make a mistake?"* If no, cut it. Other harness files target < 200.
5. **No premature quantification** — no SLO numbers / coverage % / weight tables unless you actually measure.
6. **Separate stable vs dynamic, and give every artifact a home.** Hard rules → `CLAUDE.md` (stable). In-progress work → `docs/exec-plans/active/<plan>.md` (dynamic). **Delivered plans → `completed/`. Rendered byproducts (HTML reports, teaching explainers, design variants) → `docs/exec-plans/archive/` (gitignored).** The index `docs/exec-plans/README.md` connects them. No README inside `active/` `completed/` `archive/` — the index lives one level up.
7. **Bullet + ID for evolving rules** → enables delta edits, not full rewrites (ACE paper). Rewriting a whole doc to add one rule causes *context collapse* (detail erodes) and *brevity bias* (specifics get summarized away). Add/edit small structured items instead.
8. **Prose rots; code / tests / ADRs are truth.** Never let a prose doc become a *second source of truth* for something the code, tests, or an ADR already encodes (capability lists, phase status, schema, counts). It WILL drift, and agents adopt the rotten copy as fact. State it once in the authoritative place; everywhere else **links**, not copies. **And for code-derivable facts (counts, capability lists, enums), even a hand-maintained "single source" doc rots** — derive it from code (a `gen-docs` step) or pin it with a CI assert (`assert len(REGISTRY) == N`); let prose only link. (Real case: a SPEC doc claimed "21 capabilities" while the registry had 11 — the "single source" was itself a stale literal.)
9. **Periodic self-improvement, not a runtime loop.** `/review-harness` audits on a cadence and proposes; a human approves. (A deterministic pre-commit/CI hygiene gate is a stronger opt-in for teams that want per-commit enforcement — mention it, don't scaffold it by default.)
10. **Every file earns its place** — if you can't name a concrete agent task that needs it, skip it.

---

## Workflow — 4 steps

Use TodoWrite to track: **Scan → Ask → Generate → Verify.**

### Step 1 — Scan repo silently

```bash
ls -la && git log --oneline -10 2>/dev/null
find . -maxdepth 2 -type f -name '*.md' | head -20
ls package.json pom.xml Cargo.toml pyproject.toml go.mod requirements.txt Gemfile 2>/dev/null   # stack
ls next.config.* vite.config.* nuxt.config.* 2>/dev/null                                         # framework
ls CLAUDE.md AGENTS.md GLOSSARY.md README.md docs/ .claude/ 2>/dev/null                           # existing harness
ls .github/workflows/ tests/ test/ __tests__/ 2>/dev/null                                         # maturity
```

Record internally: language/framework · existing docs worth keeping vs replacing · recent git activity (what's the team actually doing?) · maturity (PoC / MVP / prod?).

### Step 2 — Ask the user (only if uncertain)

**AskUserQuestion** for: one-sentence project description (if README is generic) · target stage (Seed/Standard/Full) · business-domain-heavy? (Y → GLOSSARY) · multi-agent tools? (Cursor+Codex+Claude → AGENTS.md naming; Claude-only → CLAUDE.md). Skip anything obvious from the scan; don't bombard.

### Step 3 — Pick stage, generate files

Read [templates.md](templates.md) now. Track each file `in_progress → completed` with TodoWrite.

**Stage 1 — Seed (any project):**
- `CLAUDE.md` (or `AGENTS.md`) — intro + Commands + Structure + Boundaries + pointer + index. **< 60 lines.**
- `docs/exec-plans/README.md` — index of active/completed/archive plans.
- Empty `docs/exec-plans/active/` + `completed/` + `archive/` dirs. **Leave active/ empty** — the owner writes their own plans. Add `docs/exec-plans/archive/` to `.gitignore`.

Stop here if the user says "just the basics."

**Stage 2 — Standard (MVP):** add `GLOSSARY.md` (if domain-heavy) · `docs/lessons.md` (seed 3-5 from git log) · `docs/exec-plans/tech-debt-tracker.md` · `docs/design-docs/core-beliefs.md` · root `README.md` (only if current is the framework default).

**Stage 3 — Full (mature), only if each earns its keep:** `ARCHITECTURE.md` (multi-module) · `DESIGN.md` (has UI) · `FRONTEND.md`/`BACKEND.md` (non-trivial split) · `RELIABILITY.md` (prod/multi-service) · `SECURITY.md` (auth/user-data) · `docs/design-docs/NNN-*.md` (decision records) · `docs/generated/` (auto-generatable schemas).

### Step 4 — Verify + hand off

- `wc -l CLAUDE.md` → **< 60** (hard; if over, slim: code blocks → tables; bullet lists → terse prose; move detail to GLOSSARY or a linked doc).
- `grep -rhoE '\]\(([^)]*\.md)\)' *.md docs/` → dead-link check (and check no link points to a gitignored path).
- Summary: what was generated, what was skipped and **why**, next steps. Suggest `/review-harness` after the next milestone.

---

## Anti-patterns

- ❌ **Dumping all 20 files at once** — offer Stage 1, confirm, move up.
- ❌ **Template placeholders left in final files** — fill or delete `<TODO>`.
- ❌ **Copying from a previous project** — scan the target first; a payments GLOSSARY is useless in a CMS.
- ❌ **Weights / percentages / SLO numbers without measurement** — false precision.
- ❌ **Promoting a lesson to a hard rule on first sighting** — require ≥2 references.
- ❌ **Prose duplicating code-truth** — a capability list / phase status / schema copied into prose becomes a second source that rots. Link to the one authoritative place.
- ❌ **Keeping ✅-delivered plans in `active/` "for reference"** — `active/` is in-progress only. Delivered → `completed/` in the **same commit** you mark it done. "Move later" = never.
- ❌ **Letting rendered HTML / reports pile up in `active/`** — they're byproducts, not plans → `archive/` (gitignored).
- ❌ **`AGENTS.md` as a symlink to `CLAUDE.md`** — Windows checks the symlink out as a plain text file containing the path, not the content. If you want both names: make **`AGENTS.md` the canonical file** and **`CLAUDE.md` a one-line `@AGENTS.md` import** — Claude Code resolves it natively, other tools read `AGENTS.md` directly, Mac+Win safe, zero-dep. For large multi-tool fan-out, [ruler](https://github.com/intellectronica/ruler) also works.
- ❌ **A four-file set in every directory** (`AGENTS.md`+`DESIGN.md`+`PROGRESS.md`+`BUGS.md` per package) — no real backing, and `PROGRESS`/`BUGS`/`DESIGN` just duplicate `exec-plans/` / `lessons.md` / ADRs (which rot independently). Use **one `AGENTS.md`** per directory that has genuinely local rules; everything else links to the single source.
- ❌ **Creating a project-level copy of `/review-harness`** — it's a user-level skill; reference it, never write `.claude/skills/review-harness/` into the target.
- ❌ **Framing the owner as "intern" / "junior" in any artifact** — harness language treats them as the owner.

---

## Closing report

```markdown
## Harness Bootstrap Done — Stage <N>
### Generated
- CLAUDE.md (<N> lines, < 60 ✓) · docs/exec-plans/README.md + active/ completed/ archive/ · <others>
### Skipped (with reason)
- ARCHITECTURE.md — single-module · RELIABILITY.md — not prod-bound
### Key conventions encoded
- Commands/Testing/Structure/Boundaries · ✅/⚠️/🚫 tiers · <N> CORRECT/INCORRECT pairs from real code · active→completed lifecycle + archive/ for byproducts
### Next steps
1. After your next milestone, run `/review-harness`
2. Append to docs/lessons.md as you hit pitfalls
3. If the project grows (UI / prod / multi-module), re-run for Stage 3
```

---

## Sources

- [AGENTS.md spec](https://agents.md/) · [GitHub 2,500-repo study](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
- [ACE paper (2510.04618)](https://arxiv.org/abs/2510.04618) — structured bullets, delta edits, context-collapse / brevity-bias
- [Harness Engineering Best Practices 2026](https://nyosegawa.com/en/posts/harness-engineering-best-practices-2026/) — mechanisms-not-prompts, feedback-speed hierarchy, "docs rot, tests don't"
- [Writing a good CLAUDE.md (HumanLayer)](https://www.humanlayer.dev/blog/writing-a-good-claude-md) — < 60 lines, pointer-not-description
- [openai/codex AGENTS.md](https://github.com/openai/codex/blob/main/AGENTS.md) · [vercel-labs/ralph-loop-agent](https://github.com/vercel-labs/ralph-loop-agent/blob/main/AGENTS.md) — CORRECT/INCORRECT reference
