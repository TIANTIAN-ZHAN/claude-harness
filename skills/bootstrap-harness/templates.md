# bootstrap-harness — file templates

Skeletons only. **Do not write verbatim** — scan the target repo and fill with real content. Placeholder syntax: `<TOKEN>`. Keep `CLAUDE.md` under 60 lines.

---

## Template: `CLAUDE.md` (< 60 lines)

```markdown
# CLAUDE.md

> **新会话从这里开始** → [`docs/exec-plans/README.md`](docs/exec-plans/README.md)(当前进度 + 恢复命令)。

<ONE-SENTENCE PROJECT DESCRIPTION — 技术栈 + 定位>

## Commands
​```bash
<install> · <build> · <test> · <run> · <lint>
​```

## Testing
- 框架:<FRAMEWORK> · **必须测**:<核心/高风险> · 可略:<CRUD / 样板>

## Project Structure
​```
<主要目录树,每目录一行;别 dump ls -R>
​```

## Code Style
- 3-5 条语言层约定 · 命名规则 · 注释只写 WHY
- 示例对照看 [`GLOSSARY.md`] 或代码(别在此堆 CORRECT/INCORRECT;放 GLOSSARY 或一个 linked doc)

## Git Workflow
- 分支命名 · commit 风格 · 不 `--force` / 不 `--no-verify` / 不自动 push
- 计划完成(✅)→ **同一个 commit** 里 mv 到 `completed/`

## Boundaries
### ✅ Always
- 在 `<main work dir>` 写代码 · 改 harness 文档
### ⚠️ Ask first
- 加依赖 · 改 schema · 改核心业务规则 · 改已上线 UX
### 🚫 Never
- 改 `<framework-core>` · 绕 auth/validation · 字符串拼 SQL · 打印密钥 · 跨服务直读对方 DB

## 模糊时默认
- <5-8 条项目特定 default,如:复用 > 创造 / 显式 > 隐式>

## harness 索引
| 要什么 | 去哪 |
|---|---|
| 当前进度 / 下一步 | `docs/exec-plans/README.md` |
| <其他已建 harness 文件 + 一句话> |

## 做完任务要更新什么
| 动了 | 更新 |
|---|---|
| 计划交付 ✅ | **同 commit** mv `active/`→`completed/` + 写"结果/踩坑/遗留" |
| 出了渲染 HTML / 报告 | 放 `docs/exec-plans/archive/`(gitignored),**别留 active/** |
| 重大决策 | 新增 `docs/design-docs/NNN-*.md` |
| 留了"先凑合" | `docs/exec-plans/tech-debt-tracker.md` |
| 可复用教训 | 追加 `docs/lessons.md` |
| 改了 code/schema | 改代码/测试本身;**别在 prose 里复述**(会烂) |

**阶段性任务完成 → 触发 `/review-harness`。**
```

> 注:若 CORRECT/INCORRECT 示例对 agent 真有用,放 `GLOSSARY.md` 或一个 linked doc,**别进 CLAUDE.md**(占行数 + 多数 task 不需要)。CLAUDE.md 是指针。

---

## Template: `docs/exec-plans/README.md`

The **single index** for all exec-plans. Plans live as `active/<plan>.md` and graduate to `completed/<plan>.md` on delivery. Rendered byproducts go to `archive/` (gitignored). **No README inside active/completed/archive.**

```markdown
# Exec Plans — 当前进度索引

> 新会话 / `/clear` 后先看这。`active/` = 进行中,`completed/` = 已交付,`archive/` = 渲染产物(gitignored,本地)。

## 快速恢复
​```bash
<切 JDK / 激活 venv / 加载 env> ; cd <root> ; <快速验证:npm run dev / pytest --co>
​```

## Active(只放进行中)
| 阶段 | 状态 | 文件 |
|---|---|---|
| <plan> | 🟡 进行中 / ⏳ 待开始 | `active/<plan>.md` |

## Completed
| 阶段 | 完成日期 | 文件 |
|---|---|---|
| <plan> | <YYYY-MM-DD> | `completed/<plan>.md` |

## 维护规则
- 进行中 → `active/<plan>.md`(active/ 里**不另写 README**)。
- 交付时:**同一个 commit** `mv active/<plan>.md completed/`,在末尾追加"结果 / 踩坑 / 遗留",同步本表。标了 ✅ 还留在 active/ = 违规。
- 渲染 HTML / 报告 / 设计变体 → `archive/`(gitignored),不进 active/。
- 阶段完成 → 触发 `/review-harness`(用户级 skill)。
```

---

## Template: `GLOSSARY.md`

```markdown
# GLOSSARY
术语、代码映射、速查。**只讲"是什么",不讲"为什么"。**

## 核心对象
| 中文 | 英文 | 代码/表 | 一句话 |
|---|---|---|---|

## 状态 / 枚举 · 复用的外部模块 · 缩写 · 关键常量
(按需建表)
```

---

## Template: `docs/lessons.md`

```markdown
# Lessons

**格式**:append-only,新条目加到对应段落顶部。`YYYY-MM-DD · 一句话 · 详细 + fix`。
**机制**:碰坑 / 完成阶段时追加。`/review-harness` 扫这里,被引用 ≥2 次的 lesson 建议提升为 CLAUDE.md 硬规则。**不记一次性小事**,只记可复用的。

## <工具链 / 环境>
- `<date>` · <summary> · <detail + fix>
## <Framework 定制 / 踩坑>
## <Harness / 文档>
```

---

## Template: `docs/exec-plans/tech-debt-tracker.md`

```markdown
# 技术债清单
**格式**:`[级别] 在哪 — 为什么先凑合 — 怎么还 — 发现日期 — revisit 触发条件`

| 级别 | 含义 |
|---|---|
| P0 | 影响上线/正确性,必须先还 |
| P1 | 影响维护,下迭代还 |
| P2 | 能忍,早晚还 |

## 活跃
### P0 / P1 / P2
(空)
## 已还(保留历史)
```

---

## Template: `docs/design-docs/core-beliefs.md`

```markdown
# 核心设计信念
做取舍时的默认值。
1. **<信念 1,如 复用 > 创造>** — <why,一段>
2. ...(5-10 条,项目特定)
```

---

## Template: project root `README.md`

```markdown
# <项目名>
<one-sentence purpose>

## 是什么 · 基于什么(fork/依赖)· 技术栈 · 当前状态
🟡 <phase>。详见 `docs/exec-plans/README.md`。

## 文档入口
| 要什么 | 看哪 |
|---|---|
| 工作守则 | `CLAUDE.md` |
| 业务术语 | `GLOSSARY.md` |
| 当前进度 | `docs/exec-plans/README.md` |

## 快速启动
<5-10 行命令>
```
