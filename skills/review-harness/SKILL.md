---
name: review-harness
description: Audit the current project's harness (CLAUDE.md + supporting docs) against recent code and git activity. Produce a markdown report only — do NOT edit files. Works in any project created by /bootstrap-harness or any repo with harness-shaped docs.
allowed-tools: Read, Grep, Glob, Bash(git log:*), Bash(git diff:*), Bash(git status:*), Bash(find:*), Bash(wc:*), Bash(ls:*)
---

# review-harness

Audit whether the project's harness docs still match the code. **Never edit files during review** — produce a report, user approves changes in a follow-up ask.

## When to invoke

- After completing a milestone / exec-plan / W-phase
- After a major architectural change
- When the user types `/review-harness`
- Periodically(e.g. 每月一次,即便没大变)

## When NOT to invoke

- In the middle of a task(context still changing)
- Right after running `/bootstrap-harness`(刚建完,没什么可审)
- 只是想改一处 → 直接改,不用走 review 流程

---

## Steps

### 1. Discover harness files (don't hardcode)

```bash
# Root-level harness
ls CLAUDE.md AGENTS.md GLOSSARY.md README.md 2>/dev/null
find . -maxdepth 1 -name '*.md' -type f
# docs/ subtree
find docs -type f -name '*.md' 2>/dev/null
# skills (user + project)
ls ~/.claude/skills/ .claude/skills/ 2>/dev/null
```

Read whatever exists — don't assume a fixed file list. Different projects have different subsets (Seed / Standard / Full bootstrap stages).

### 2. Scan current project state

```bash
git log --oneline -20
git diff HEAD~5 --stat 2>/dev/null  # if there are 5+ commits
git status --short
# Code size — infer main source dir from presence of package.json / pom.xml / go.mod etc.
find <main-source-dir> -type f \( -name '*.java' -o -name '*.ts' -o -name '*.py' -o -name '*.go' \) | wc -l
```

Adapt commands to the stack (Java → `*.java`, TS → `*.ts`, etc.).

### 3. Compare harness claims vs reality

For each harness file found, ask:

- **Still accurate?** Does a command listed in CLAUDE.md still work? Does ARCHITECTURE mention a module that was removed? Does `active/README.md` say "W1 ongoing" when it's done?
- **Missing from harness?** New pattern in recent commits not reflected?
- **Redundant?** Same rule repeated in 2+ files?
- **Graduate candidate?** A lesson in `docs/lessons.md` referenced in ≥2 places → candidate to promote to CLAUDE.md hard rule
- **Dead?** File that nothing links to AND no recent edit → archive candidate

### 4. Output this exact report format

```markdown
# Harness Review (<YYYY-MM-DD>)

## 📊 Stats
- CLAUDE.md (or AGENTS.md): N 行(**硬上限 < 150**,超了必须在 ⚠️ Needs update 里给具体精简建议)
- Total harness files: N
- Active exec-plans: N
- Open tech debt: P0:N / P1:N / P2:N
- Lessons total / since last review: N / M
- Source files: N (<ext>)

## ✅ Still accurate(抽检样本)
- `<file>`: <一句话 confirm>

## ⚠️ Needs update
- `<file>:<line>`: <what's stale> → <old → new 精确建议>

## 📝 Missing in harness
- <从代码/git 观察到的> → <该加到哪个文件的哪一段>

## 🔁 Merge / consolidation candidates
- `<A>` §X and `<B>` §Y 都说 Z → consolidate to <target>,删另一处

## 🎯 Promote from lessons
- `docs/lessons.md: <date> <topic>` (referenced N 次) → `CLAUDE.md` §<section>

## 💀 Dead / archive candidates
- `<file>`: 无 link + 最近 N 天无编辑 → 建议归档到 `docs/archive/` 或删

## Suggested next actions(按 ROI 排序)
1. <action,具体改哪个文件的哪一段>
2. ...
```

### 5. Stop

Do NOT apply changes. Wait for user to approve or adjust the report, **then** they'll ask you to implement specific items.

---

## Constraints

- **No file edits during review** — only Read / Grep / Glob / Bash(read-only)
- **Don't promote a lesson unless referenced ≥2 times** — avoid over-fitting to one-off observations
- **Be honest about ✅** — don't pad. 如果全对,1-2 项抽检足够
- **Report < 500 行** — 超长时把低优先项 bucket 成附录
- **CLAUDE.md 硬上限 150 行** — 超了时 ⚠️ Needs update 里必须给**具体精简建议**(哪段合并 / 哪段移到 GLOSSARY / 哪几条 boundary 合并),**不只是"太长"**。参考做法:代码块对照改 markdown 表格;bullet list 改流水短句;`---` 分隔符按需保留
- **Tie findings to business impact** — "agent 会因此犯什么错" 不是"不整齐"
- **No new quantitative metrics** — 不要在 review 里重新引入 SLO 数字 / coverage %,即便发现项目里没有;那是刻意的设计

---

## Anti-patterns

- ❌ Review 后立即改文件(违反"先讨论再改")
- ❌ Promote 每一条 lesson(lessons 是沉淀,不是升级候选队列)
- ❌ 建议全文重写(delta edit,不 full rewrite — ACE 论文启示)
- ❌ 加新的量化指标(覆盖率 / p99 / 权重表)
- ❌ 硬编码特定项目的文件列表(用 Step 1 的 discovery 机制,让 skill 在任何项目都能跑)
