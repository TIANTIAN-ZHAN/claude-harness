# Claude Harness

[简体中文](README.md) · **English**

**Scaffold a production AI agent harness in any repo — in 5 minutes.**

*Tools for the post-vibe-coding era.*

In February 2025, Andrej Karpathy [coined "vibe coding"](https://x.com/karpathy/status/1886192184808149383): *"fully give in to the vibes, embrace exponentials, and forget that the code even exists."* By December, Collins named it [Word of the Year](https://en.wikipedia.org/wiki/Vibe_coding). By Q1 2026, Karpathy himself called it [*passé*](https://thenewstack.io/vibe-coding-is-passe/).

The data caught up. [96% of developers](https://earezki.com/ai-news/2026-04-26-vibe-coding-just-failed-its-first-real-audit/) don't trust AI-generated code. [45%](https://www.veracode.com/) of it ships with security flaws. [16 of 18 CTOs](https://dev.to/paulthedev/the-vibe-coding-hangover-is-real-what-nobody-tells-you-about-ai-generated-code-in-production-399h) reported production disasters. Salesforce is calling 2026 [the year of technical debt](https://www.salesforceben.com/2026-predictions-its-the-year-of-technical-debt-thanks-to-vibe-coding/).

OpenAI and Martin Fowler have a name for what comes next: **[harness engineering](https://openai.com/index/harness-engineering/)** — the rules, context, and feedback loops that make agents reliable.

This is the toolkit. Five skills, five minutes per repo.

---

## The five skills

| Skill | Treats | When |
|---|---|---|
| `/bootstrap-harness` | A repo with no rulebook | Fresh project · no CLAUDE.md · major refactor |
| `/test-first` | Code no test ever pinned down | Building a feature · fixing a bug |
| `/review-harness` | Docs that drift from code | After a milestone · monthly |
| `/finish-phase` | Sprints that never close out | A milestone ships |
| `/promote-lesson` | The same mistake, twice | Same lesson referenced ≥ 2 times |

Four of them maintain the harness's *rules and context* (the docs system). `/test-first` drives its tightest *feedback loop* — **write the test before the feature**: pin down what the code should do with a failing test, then write just enough to turn it green (the red-green cycle). The agent never ships code no test ever checked.

## Install

```bash
/plugin marketplace add TIANTIAN-ZHAN/claude-harness
/plugin install claude-harness@TIANTIAN-ZHAN
```

Or copy into `~/.claude/skills/`:

```bash
git clone https://github.com/TIANTIAN-ZHAN/claude-harness ~/.claude/skills-tmp
cp -r ~/.claude/skills-tmp/skills/* ~/.claude/skills/
```

## What `/bootstrap-harness` builds

A docs system the agent reads before doing anything:

- **`CLAUDE.md`** — commands, conventions, three-tier boundaries (✅ Always / ⚠️ Ask first / 🚫 Never). Capped at 60 lines so it stays in context cheaply.
- **`docs/exec-plans/`** — `active/` for in-flight work, `completed/` for shipped. The index file becomes the new-session entry point: open it and the agent knows where you left off.
- **`scripts/check-harness.mjs`** — four zero-dep mechanical gates (status line / lifecycle / two-way index / link health), wired into CI from day one; repos with CI also get a weekly self-check workflow that notifies on green AND red. Prose only stays true when something checks it.
- **`docs/lessons.md`** — append-only post-mortems. The raw material `/promote-lesson` mines.
- **`docs/exec-plans/tech-debt-tracker.md`** — the "we'll come back to this" list, prioritized P0–P3.
- Optionally: `GLOSSARY.md`, `ARCHITECTURE.md`, `core-beliefs.md` — only when the project earns them.

Three progressive stages — **Seed** for any repo (~5 minutes), **Standard** for MVP-level (+30 minutes), **Full** for mature systems (+2 hours). Stop wherever fits your project's maturity.

## How it's built

Encodes three pieces of research from the past year — the [AGENTS.md spec](https://agents.md/) (stewarded by the Linux Foundation, used by 60,000+ projects), [GitHub's 2,500-repo study](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/) on what AGENTS.md / CLAUDE.md files actually work in the wild, and the [ACE paper](https://arxiv.org/abs/2510.04618) on structured-bullet + delta-edit patterns for evolving rulebooks (the basis for `/promote-lesson`).

MIT licensed.
