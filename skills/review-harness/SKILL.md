---
name: review-harness
description: Audit a project's harness (CLAUDE.md + supporting docs) against current code and git activity, and catch lifecycle/hygiene drift (completed work stuck in active/, rendered byproducts piling up, prose docs gone stale, broken links). Produces a markdown report only — never edits files. Use after a milestone, after a major change, on a periodic cadence, or when the user runs /review-harness. Works in any repo with harness-shaped docs.
allowed-tools: Read, Grep, Glob, Bash(git log:*), Bash(git diff:*), Bash(git status:*), Bash(find:*), Bash(wc:*), Bash(ls:*), Bash(grep:*)
---

# review-harness

Audit whether the harness still matches reality, and whether it's drifting structurally. **Never edit during review** — produce a report; the user approves changes in a follow-up.

This skill is the harness's **slow lane** — it catches what a machine gate can't: stale prose, structural drift, second-source rot. Mechanically checkable invariants (counts, lifecycle moves, link validity) belong in a **deterministic CI / pre-commit gate**, not a periodic human review — so part of this review's job is to spot invariants caught *only* here and **recommend gating them** (derive-from-code + `git diff --exit-code`) so they stop recurring. Run on a cadence (`/schedule`) or after each milestone — output should be issue/PR-ready. *(A project with no gate yet: this review is the primary catch until one exists.)*

## When to invoke

- After a milestone / exec-plan / phase · after a major architectural change · on a periodic schedule · when the user types `/review-harness`.

## When NOT to invoke

- Mid-task (context still changing) · right after `/bootstrap-harness` (nothing to audit) · a single one-off fix (just make it).

---

## Steps

### 1. Discover harness files (don't hardcode)

```bash
ls CLAUDE.md AGENTS.md GLOSSARY.md README.md 2>/dev/null
find . -maxdepth 1 -name '*.md' -type f
find docs -type f -name '*.md' 2>/dev/null
ls docs/exec-plans/active/ docs/exec-plans/completed/ docs/exec-plans/archive/ 2>/dev/null
```

Read whatever exists — projects have different subsets (Seed / Standard / Full).

### 2. Scan current state

```bash
git log --oneline -20
git diff HEAD~5 --stat 2>/dev/null
git status --short
# code size — infer source dir from package.json / pom.xml / go.mod / pyproject.toml, then count *.ts/*.py/*.java/*.go
```

### 3. Compare harness claims vs reality — and run the hygiene checks

**Content drift (semantic — read and judge):**
- **Still accurate?** Does a listed command still work? Does a doc describe a module / count / phase that changed (e.g. "7 capabilities" when the code has 15)?
- **Missing?** A pattern in recent commits reflected nowhere.
- **Prose-as-second-truth (high priority).** Any prose doc duplicating something code / tests / ADRs already own (capability list, schema, phase status, counts)? If its copy now contradicts reality, that's the rot — flag it and name the contradiction. Fix is usually "delete the stale/duplicated part, link to the authoritative source," not "update both copies."

**Lifecycle & hygiene drift (mostly mechanical — grep / ls):**
- **Completed stuck in `active/`** — any `active/<plan>.md` marked `✅`/done/完成 but never moved to `completed/`. (The single most common rot: ✅ feels done, the move gets skipped.)
- **Byproducts in `active/`** — rendered `.html`, reports, design variants sitting in `active/` instead of `archive/`.
- **Broken / dangling links** — tracked `.md` linking to a file that's missing or gitignored (clones 404).
- **Cross-doc duplication** — the same block (project-essence, pitfalls, phase table) maintained in 2+ docs with no link → one will rot.
- **Ungated invariant** — a mechanical check above (count vs code, ✅→`completed/` move, link validity) enforced *only* by this periodic review, with no CI / pre-commit gate. Recommend deriving + gating it (assert count from code, `git diff --exit-code` on generated docs, a lifecycle assertion) so it can't drift between reviews.

**Graduate / dead:**
- **Graduate candidate** — a `docs/lessons.md` entry referenced in ≥2 places → propose promoting to a CLAUDE.md hard rule.
- **Dead** — a file nothing links to AND no recent edit → archive candidate.

### 4. Output this report format

```markdown
# Harness Review (<YYYY-MM-DD>)

## 📊 Stats
- CLAUDE.md (or AGENTS.md): N lines (**hard limit < 60**; if over, the ⚠️ section MUST give concrete slimming moves)
- Total harness files: N · Active exec-plans: N · Completed: N · Open tech debt: N
- Lessons total / since last review: N / M · Source files: N (<ext>)

## ✅ Still accurate (spot-check)
- `<file>`: <one-line confirm>

## 🧹 Lifecycle & hygiene  ← the mechanical drift this review exists to catch
- **✅-stuck-in-active**: `active/<plan>.md` marked done → should be in `completed/` (or "none")
- **byproduct-in-active**: `active/<x>.html` → should be in `archive/` (or "none")
- **broken-link**: `<file>:<line>` → missing/gitignored `<target>` (or "none")

## ⚠️ Needs update
- `<file>:<line>`: <what's stale> → <precise old → new>

## 📝 Missing in harness
- <observed in code/git> → <which file/section should hold it>

## 🔁 Duplication / consolidation
- `<A>` and `<B>` both state Z (no link) → keep in <authoritative>, link from the other

## 🚦 Gate candidates (move from slow lane → CI)
- `<invariant>` caught only by this review → derive + gate (`<how — e.g. assert len(REGISTRY)==N / git diff --exit-code on generated docs>`) so it stops recurring

## 🎯 Promote from lessons
- `docs/lessons.md: <entry>` (referenced N times) → `CLAUDE.md` §<section>

## 💀 Dead / archive candidates
- `<file>`: no inbound link + no edit in N days → archive/delete

## Suggested next actions (by ROI)
1. <action — which file, which section> ...
```

### 5. Stop

Do NOT apply changes. Wait for the user to approve, then they ask you to implement specific items.

---

## Constraints

- **No file edits during review** — Read / Grep / Glob / Bash (read-only) only.
- **CLAUDE.md hard limit < 60 lines.** If over, the ⚠️ section MUST give *specific* slimming moves (which block → table, which → GLOSSARY / linked doc, which boundaries to merge) — not just "too long."
- **Don't promote a lesson unless referenced ≥2 times** — avoid over-fitting to a one-off.
- **Be honest about ✅** — don't pad. If clean, 1-2 spot-checks suffice; write "none" in empty hygiene categories.
- **Report < 500 lines** — bucket low-priority items into an appendix if long.
- **Tie findings to business impact** — "the agent will make mistake X," not "it's untidy."
- **No new quantitative metrics** — don't introduce SLO numbers / coverage % the project deliberately omits.
- **Delta, not rewrite** — propose targeted edits; never suggest rewriting a doc wholesale (ACE: full rewrites cause context collapse).

---

## Anti-patterns

- ❌ Editing files during review (the whole point is propose-then-approve).
- ❌ Promoting every lesson (lessons are sediment, not a promotion queue).
- ❌ Suggesting full-doc rewrites instead of delta edits.
- ❌ Adding new quantitative metrics the project chose to omit.
- ❌ Hardcoding a fixed file list — use Step 1 discovery so this runs in any repo.
- ❌ Reporting hygiene categories as prose paragraphs — keep them as the grep-backed bullet lists above so they're scannable and actionable.
