# Claude Harness

**简体中文** · [English](README.en.md)

**在任何仓库里搭起一套生产级 AI agent harness —— 5 分钟搞定。**

*为「后 vibe coding 时代」准备的工具。*

2025 年 2 月,Andrej Karpathy [造出了「vibe coding」这个词](https://x.com/karpathy/status/1886192184808149383):*「彻底交给感觉,拥抱指数级增长,忘掉代码本身的存在。」* 到 12 月,柯林斯词典把它[选为年度词汇](https://en.wikipedia.org/wiki/Vibe_coding)。再到 2026 年第一季度,Karpathy 自己又改口说它[过时了](https://thenewstack.io/vibe-coding-is-passe/)。

数据随后印证了这一点。[96% 的开发者](https://earezki.com/ai-news/2026-04-26-vibe-coding-just-failed-its-first-real-audit/)不信任 AI 生成的代码。其中 [45%](https://www.veracode.com/) 带着安全漏洞上了线。[18 位 CTO 里有 16 位](https://dev.to/paulthedev/the-vibe-coding-hangover-is-real-what-nobody-tells-you-about-ai-generated-code-in-production-399h)报告了生产环境事故。Salesforce 干脆把 2026 年叫做[技术债之年](https://www.salesforceben.com/2026-predictions-its-the-year-of-technical-debt-thanks-to-vibe-coding/)。

对于接下来该怎么办,OpenAI 和 Martin Fowler 给了个名字:**[harness engineering](https://openai.com/index/harness-engineering/)** —— 那套让 agent 变得可靠的规则、上下文与反馈闭环。

这就是那套工具。五个 skill,一个仓库五分钟。

---

## 五个 skill

| 技能 | 治什么 | 何时用 |
|---|---|---|
| `/bootstrap-harness` | 仓库没有规则手册 | 新项目 · 没有 CLAUDE.md · 大重构 |
| `/test-first` | 没有任何测试钉死的代码 | 写功能 · 修 bug |
| `/review-harness` | 文档和代码逐渐脱节 | 里程碑后 · 每月 |
| `/finish-phase` | 永远收不了尾的迭代 | 里程碑交付时 |
| `/promote-lesson` | 同一个错犯第二遍 | 同一条教训被引用 ≥ 2 次 |

其中四个维护 harness 的*规则与上下文*(那套文档系统)。`/test-first` 驱动它最紧的*反馈闭环* —— **写功能之前,先把测试写好**:用一个失败的测试钉死「这段代码该做什么」,再写刚好让它变绿的实现(red-green 循环)。这样 agent 交不出没有测试验证过的代码。

## 安装

```bash
/plugin marketplace add TIANTIAN-ZHAN/claude-harness
/plugin install claude-harness@TIANTIAN-ZHAN
```

或者直接拷进 `~/.claude/skills/`:

```bash
git clone https://github.com/TIANTIAN-ZHAN/claude-harness ~/.claude/skills-tmp
cp -r ~/.claude/skills-tmp/skills/* ~/.claude/skills/
```

## `/bootstrap-harness` 会建出什么

一套 agent 动手前先读的文档系统:

- **`CLAUDE.md`** —— 命令、约定、三级边界(✅ 总是 / ⚠️ 先问 / 🚫 绝不)。控制在 60 行以内,这样每次进上下文都很便宜。
- **`docs/exec-plans/`** —— `active/` 放进行中的工作,`completed/` 放已交付的。索引文件就是新会话的入口:打开它,agent 就知道你上次停在哪。
- **`docs/lessons.md`** —— 只增不改的复盘记录,是 `/promote-lesson` 挖料的原矿。
- **`docs/exec-plans/tech-debt-tracker.md`** —— 「回头再收拾」清单,按 P0–P3 排优先级。
- 可选:`GLOSSARY.md`、`ARCHITECTURE.md`、`core-beliefs.md` —— 只在项目确实需要时才建。

三个递进阶段 —— **Seed** 适合任何仓库(约 5 分钟),**Standard** 适合 MVP 级(再加 30 分钟),**Full** 适合成熟系统(再加 2 小时)。在匹配你项目成熟度的地方停下就行。

## 它的依据

把过去一年的三项研究固化了下来 —— [AGENTS.md 规范](https://agents.md/)(由 Linux 基金会托管,6 万多个项目在用)、[GitHub 对 2,500 个仓库的研究](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)(真实世界里哪些 AGENTS.md / CLAUDE.md 文件真的管用),以及 [ACE 论文](https://arxiv.org/abs/2510.04618)(用结构化条目 + 增量编辑来演进规则手册的范式,也是 `/promote-lesson` 的依据)。

MIT 协议。
