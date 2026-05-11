---
name: bootstrap-harness
description: Create a complete AI-agent harness (CLAUDE.md + supporting docs system) for any project. Portable across Python/Java/TS/Go. Based on 2025-2026 best practices (AGENTS.md spec, GitHub's 2,500-repo study, ACE paper). Works in 3 progressive stages — stop at any level.
allowed-tools: Read, Glob, Grep, Write, Edit, Bash, AskUserQuestion, TodoWrite
---

# bootstrap-harness

Create a production-quality harness (docs system for AI agents) in any repo. Uses **3 progressive stages** — produce only what the project actually needs at its current maturity.

## When to use

- **Fresh repo / no CLAUDE.md**: user just cloned or scaffolded, wants AI-agent workflow
- **Repo with no harness**: team wants to adopt structured AI coding
- **Major refactor of existing harness**: current docs are bloated / outdated

## When NOT to use

- **Minor tweaks**: use `/review-harness` skill instead (incremental)
- **Single-file script / experiment**: overkill
- **Harness already exists and mostly accurate**: run `/review-harness` first to confirm

---

## Core principles (2025-2026 research)

1. **Commands go first** — agents query build/test/run most often (GitHub 2,500-repo study)
2. **✅ / ⚠️ / 🚫 three-tier Boundaries** — Always / Ask first / Never (AGENTS.md spec)
3. **CORRECT / INCORRECT code pairs** — concrete examples beat prose (vercel-labs/ralph-loop-agent pattern)
4. **CLAUDE.md hard limit < 150 lines** — stricter than Anthropic's < 200 baseline,确保 agent 每次会话 context 成本最低 · 其他 harness 文件目标 < 200
5. **No premature quantification** — avoid SLO numbers / coverage % / weight tables unless you measure
6. **Separate stable vs dynamic** — hard rules in CLAUDE.md (stable); current progress in `docs/exec-plans/active/<plan>.md` (dynamic); the index `docs/exec-plans/README.md` connects them. **No README inside `active/` or `completed/`** — index lives one level up.
7. **Bullet + ID for evolving rules** — enables delta edits not full rewrites (ACE paper)
8. **PR-level self-improvement, not runtime loop** — `/review-harness` proposes, human approves
9. **Every file must earn its place** — if you can't name a concrete agent task that needs it, skip

---

## Workflow — 4 steps

### Step 1 — Scan repo silently

Use TodoWrite to track: Scan → Ask → Generate → Verify.

Run in parallel:

```bash
# Fingerprint
ls -la && git log --oneline -10 2>/dev/null
find . -maxdepth 2 -type f -name '*.md' | head -20
# Tech stack hints
ls package.json pom.xml Cargo.toml pyproject.toml go.mod requirements.txt Gemfile 2>/dev/null
# Framework markers
ls next.config.* vite.config.* nuxt.config.* 2>/dev/null
# Existing harness
ls CLAUDE.md AGENTS.md GLOSSARY.md README.md docs/ .claude/ 2>/dev/null
# Maturity signals
ls .github/workflows/ tests/ test/ __tests__/ 2>/dev/null
```

Record (internally):
- Language / framework
- Existing docs worth preserving vs replacing
- Recent git activity (what's team actually doing?)
- Maturity (PoC / MVP / prod?)

### Step 2 — Ask the user (only if uncertain)

Use **AskUserQuestion** for:
- **One-sentence project description** (if README is generic/empty)
- **Target stage**: Seed / Standard / Full (see below)
- **Business-domain heavy?** (Y → needs GLOSSARY; N → skip)
- **Multi-agent tools?** (if user uses Cursor + Codex + Claude Code → recommend AGENTS.md naming; if Claude-only → CLAUDE.md)

Skip questions obvious from scan. Don't bombard.

### Step 3 — Pick stage, generate files

Use TodoWrite to track each file as `in_progress` → `completed`.

#### Stage 1 — Seed (5-10 min, any project)

Must produce:
- `CLAUDE.md` (or `AGENTS.md`) — project intro + Commands + Structure + Boundaries + pointer + index
- `docs/exec-plans/README.md` — index of active/ + completed/ plans (do NOT put README inside `active/` or `completed/`)
- Empty `docs/exec-plans/active/` directory — **leave it empty; the project owner writes their own plans here**. Bootstrap creates the directory and the index, NOT individual plan content. If the user has already discussed a plan with you in the same conversation, that's still their work to write — do not auto-fill `active/<plan>.md` from the conversation.

This is the minimum. Stop here if user says "just the basics".

**Note on `/review-harness`**: it's a **user-level skill**(`~/.claude/skills/review-harness/`),shared across all projects. Bootstrap does **NOT** create a project-level copy. CLAUDE.md can reference `/review-harness` directly. If the user doesn't have it installed, tell them to install it once and it'll be available in every project.

#### Stage 2 — Standard (+30-60 min, MVP-level)

Add to Stage 1:
- `GLOSSARY.md` (if business-domain heavy)
- `docs/lessons.md` (append-only, seed with 3-5 early observations from git log / README discussion)
- `docs/exec-plans/tech-debt-tracker.md`
- `docs/design-docs/core-beliefs.md`
- Project-root `README.md` (only if current is framework's default / empty / belongs to forked upstream)

#### Stage 3 — Full (+2-4 hours, mature projects)

Add to Stage 2, **only if each earns its keep**:
- `ARCHITECTURE.md` — required if multi-module
- `DESIGN.md` — required if has user-facing UI
- `FRONTEND.md` / `BACKEND.md` — only if front/back split is non-trivial
- `RELIABILITY.md` — only if prod-bound / multi-service
- `SECURITY.md` — only if has auth / user data / secrets
- `PRODUCT_SENSE.md` — only if product decisions are frequent
- `PLANS.md` — only if team writes plans often
- `QUALITY_SCORE.md` — **qualitative only**, no weights, no SLO numbers
- `docs/product-specs/` — one per major feature
- `docs/design-docs/NNN-*.md` — numbered decision records
- `docs/generated/` — schemas if auto-generatable

### Step 4 — Verify + hand off

- `wc -l CLAUDE.md` < 150 (**hard limit**; if over,必须精简:代码块对照 → markdown 表格;bullet list → 流水短句;或移内容到 GLOSSARY.md)
- `grep -rh '\](.*\.md)' *.md docs/` → dead-link check
- Show user a summary: what was generated, what was skipped and why, next steps
- Suggest running `/review-harness` after next milestone

---

## Templates

**These are skeletons. Do not write them verbatim — scan the target repo and fill with real content.** Placeholder syntax: `<TOKEN>`.

### Template: `CLAUDE.md`

```markdown
# CLAUDE.md

> **新会话从这里开始**:[`docs/exec-plans/README.md`](docs/exec-plans/README.md) — 当前进度 + 恢复命令。

<ONE-SENTENCE PROJECT DESCRIPTION,含技术栈和定位>

<如有 GLOSSARY:**业务术语看** [`GLOSSARY.md`](GLOSSARY.md) · **边界清单看本文档底部**。>

---

## Commands

​```bash
<install / setup>
<build>
<test>
<run>
<lint / format>
​```

---

## Testing

- 框架:<FRAMEWORK>
- **必须测**:<核心/高风险模块>
- 可略:<CRUD / 样板 / 简单 UI>

---

## Project Structure

​```
<主要目录树,每目录一行说明,别 dump 整个 ls -R>
​```

---

## Code Style

- 语言层约定(简短,3-5 条)
- 命名规则
- 注释规则:默认不写,只写 WHY

### 示例(✅ CORRECT vs ❌ INCORRECT)

至少 3 组真实对照,**从仓库现有代码挑**,别造。每组:
- 一个清晰命名/结构
- 一个反例(自创缩写 / 违反模式 / 直连内部)

---

## Git Workflow

- 分支命名
- Commit 风格(语言 / prefix)
- 不 `--force push` / 不 `--no-verify` / 不自动 push
- 完成阶段 → 更新对应 active plan

---

## Boundaries

### ✅ Always
- 在 `<main work dir>` 新增代码
- 改 harness 文档
- <其他项目特定>

### ⚠️ Ask first
- 加新依赖(尤其 >5MB)
- 改 schema(字段类型 / 加大表字段)
- 改核心业务规则
- 改已上线的用户体验

### 🚫 Never
- 改 `<framework-core>` / 被禁目录
- 绕安全检查(auth / authz / validation)
- 用字符串拼接 SQL(注入)
- 打印密码/token 到日志
- 直接跨模块/服务读对方的 DB / 私有 API

---

## 模糊时的默认选择

- <5-8 条项目特定 defaults,如:复用 > 创造 / 硬编码 > 配置 / 显式 > 隐式>

---

## harness 索引

| 问题 | 去哪里 |
|---|---|
| 当前进度 / 下一步 | `docs/exec-plans/README.md` |
| <其他已创建的 harness 文件> |

---

## 做完任务要更新什么

| 动了什么 | 要更新 |
|---|---|
| 数据库 schema | `docs/generated/db-schema.md`(如有) |
| 重大技术决策 | 新增 `docs/design-docs/NNN-*.md` |
| exec-plan 完成 | mv 到 `completed/` + 写"结果/踩坑/遗留" |
| 留了"先凑合" | `docs/exec-plans/tech-debt-tracker.md` |
| 学到可复用教训 | 追加 `docs/lessons.md` |

**完成阶段性任务时触发 `/review-harness`**。
```

### Template: `docs/exec-plans/README.md`

This is the **single index** for all exec-plans. Individual plans live as `docs/exec-plans/active/<plan-name>.md` and graduate to `docs/exec-plans/completed/<plan-name>.md` when delivered. **Do not write a `README.md` inside `active/` or `completed/`** — the index here is authoritative.

```markdown
# Exec Plans — 当前进度索引

> 新会话 / `/clear` 后先看这。`active/` 下是进行中的 plan,`completed/` 下是已交付的历史 plan。

## 快速恢复

​```bash
<机器特定命令:切 JDK / 激活 venv / 加载 env / source 脚本>
cd <project-root>
<快速验证命令:如 npm run dev / mvn compile / pytest --co>
​```

## Active

| 阶段 | 状态 | 文件 |
|---|---|---|
| <plan name> | 🟢 完成 / 🟡 进行中 / ⏳ 待开始 | `active/<plan-file>.md` |

## Completed

| 阶段 | 完成日期 | 文件 |
|---|---|---|
| <past plan> | <YYYY-MM-DD> | `completed/<plan-file>.md` |

## 下一步

<link or description>

## 维护规则

- 进行中的 plan 放 `active/<plan-name>.md`,**不要在 `active/` 里另写 `README.md`**。
- 阶段交付时 `mv active/<plan>.md completed/<plan>.md` 并在 plan 末尾追加 "结果 / 踩坑 / 遗留" 段。
- 同时更新本文件的 Active / Completed 表格。
- 完成阶段触发 `/review-harness`(用户级 skill,跨项目可用)审查 harness。
```

### Template: `GLOSSARY.md`

```markdown
# GLOSSARY

记录术语、代码映射、速查。**本文件只讲"是什么",不讲"为什么"**。

## 核心对象

| 中文 | 英文 | 代码/表 | 说明 |
|---|---|---|---|
| <biz term> | <English term> | `<table / class>` | <one-line> |

## 状态 / 枚举

| 对象 | 状态 | 值 |
|---|---|---|

## 复用的外部对象 / 模块

| 对象 | 在哪 | 我们怎么用 |
|---|---|---|

## 缩写

| 缩写 | 全称 |
|---|---|

## 关键常量 / 约定

| 参数 | 默认 | 存在哪 |
|---|---|---|
```

### Template: `docs/lessons.md`

```markdown
# Lessons — 教训 / 学到的东西

**格式**:append-only,新条目加到对应段落**顶部**。\`YYYY-MM-DD · 一句话 · 详细\`。

**机制**:碰到坑或完成阶段时追加。`/review-harness` 扫这个文件,referenced ≥2 次的 lesson 建议提升为 CLAUDE.md 硬规则。**不记一次性小事**,只记有可复用价值的。

---

## <Category 1:工具链 / 环境>

- `<date>` · <summary> · <detail with fix>

## <Category 2:Framework 定制 / 踩坑>

## <Category 3:Harness / 文档>

## <Category 4:业务 / 非代码>(可选)
```

### Template: `docs/exec-plans/tech-debt-tracker.md`

```markdown
# 技术债清单

**格式**:`[优先级] 在哪里 — 为什么先凑合 — 怎么还 — 发现日期`

| 级别 | 含义 |
|---|---|
| P0 | 影响上线 / 正确性,必须先还 |
| P1 | 影响维护,下个迭代还 |
| P2 | 能忍,但早晚要还 |
| P3 | 理论上能优化,不急 |

## 活跃
### P0
(空)
### P1
### P2
### P3

## 已还(保留历史)
(空)
```

### Template: `docs/design-docs/core-beliefs.md`

```markdown
# 核心设计信念

做取舍时,这些是默认值。

1. **<信念 1,如:复用 > 创造>** — <why,一段>
2. **<信念 2,如:硬编码 > 过度配置>** — <why>
3. ...(5-10 条,项目特定)
```

### Template: project root `README.md`

```markdown
# <项目名>

<one-sentence purpose>

## 是什么

<2-4 行功能清单>

## 基于什么(如 fork / 依赖)

<上游 / 主要依赖 / license 继承>

## 技术栈

<一行技术列表>

## 当前状态

🟡 <current phase>。详见 `docs/exec-plans/README.md`。

## 文档入口

| 需要什么 | 看哪里 |
|---|---|
| 工作守则 | `CLAUDE.md` |
| 业务术语 | `GLOSSARY.md` |
| 架构 | `ARCHITECTURE.md` |
| 当前进度 | `docs/exec-plans/README.md` |

## 快速启动

<5-10 行命令>

## 许可证
```

---

## Anti-patterns

- ❌ **Dumping all 20 files at once** — user overwhelmed. Offer Stage 1, confirm, then move up.
- ❌ **Template placeholders left in final files** — if `<TODO: fill>` survives to user, they'll never fill it. Fill or delete.
- ❌ **Copying from a previous project** — ofct's GLOSSARY is useless in a payments app. Scan the target first.
- ❌ **Adding weights / percentages / SLO numbers without measurement** — false precision.
- ❌ **Creating empty `docs/references/`** — only create when there's real content to put there.
- ❌ **Promoting a lesson to hard rule on first observation** — require ≥2 references.
- ❌ **Writing `AGENTS.md` as a symlink to `CLAUDE.md` (or vice versa)** — Windows-incompatible. Pick one, stick with it. If user needs both for multi-agent, use [ruler](https://github.com/intellectronica/ruler) tool.
- ❌ **Creating a project-level copy of `/review-harness`** — it's a user-level skill. Bootstrap should reference `/review-harness` in CLAUDE.md but never write `.claude/skills/review-harness/SKILL.md` in the target project.
- ❌ **Referencing skills / hooks the user doesn't have** — if you mention `/review-harness` in CLAUDE.md, confirm the user has `~/.claude/skills/review-harness/` installed; if not, tell them to install it once.
- ❌ **Writing `README.md` inside `docs/exec-plans/active/` or `docs/exec-plans/completed/`** — the index lives at `docs/exec-plans/README.md` (one level up). Subfolders only hold individual plan files, never their own README. Same principle applies to other harness subfolders unless they have a clear independent index need.
- ❌ **Pre-filling active plan content during bootstrap** — even if the user discussed a plan in the same conversation, leave `active/` empty. Bootstrap delivers the *scaffold* (CLAUDE.md, GLOSSARY, design-docs, product-specs, lessons, tech-debt-tracker, core-beliefs, exec-plans index); the project owner writes their own active plans. If you write a sprint/plan into `active/`, you've taken authorship away from the owner.
- ❌ **Framing the project owner as "intern" / "junior" / "the user's intern" in any artifact** — even when the user explicitly tells you so, do not encode that framing into project-visible files. The project belongs to whoever owns it; harness language should treat them as the owner. (The user-level memory may keep relational context, but project artifacts must not.)

---

## Closing report format

When done, output to user:

```markdown
## Harness Bootstrap Done — Stage <N>

### Generated
- CLAUDE.md (N lines)
- docs/exec-plans/README.md (index of active/ + completed/)
- docs/exec-plans/active/<plan>.md
- <other files>

(Not generated: `.claude/skills/review-harness/SKILL.md` — it's a user-level skill, shared across projects.)

### Skipped (with reason)
- ARCHITECTURE.md — single-module project, not needed yet
- RELIABILITY.md — not prod-bound

### Key conventions encoded
- Commands/Testing/Structure/Boundaries in CLAUDE.md (< 150 lines)
- ✅/⚠️/🚫 three-tier Boundaries
- N CORRECT/INCORRECT code pairs from real repo code

### Next steps
1. After your next milestone, run `/review-harness` to audit
2. Append lessons to `docs/lessons.md` as you hit them
3. If project grows (adds UI / prod / multi-module), run `/bootstrap-harness` again to lift to Stage 3
```

---

## Sources

- [AGENTS.md spec](https://agents.md/)
- [GitHub 2,500-repo study](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
- [openai/codex AGENTS.md](https://github.com/openai/codex/blob/main/AGENTS.md) — "what NOT to do" reference
- [vercel-labs/ralph-loop-agent AGENTS.md](https://github.com/vercel-labs/ralph-loop-agent/blob/main/AGENTS.md) — CORRECT/INCORRECT pair reference
- [ACE paper (2510.04618)](https://arxiv.org/abs/2510.04618) — structured bullets + delta edits + helpful/harmful counters
- [Agent drift analysis](https://prassanna.io/blog/agent-drift/) — why fully-autonomous self-maintenance fails
