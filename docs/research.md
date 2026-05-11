# Research

What this toolkit cites and why. All sources are publicly verifiable.

## The harness engineering thesis

| Source | Why it matters |
|---|---|
| [Harness engineering — OpenAI](https://openai.com/index/harness-engineering/) | Codex team's own framing of the discipline. Officially coined the term. |
| [Harness engineering for coding agent users — Martin Fowler](https://martinfowler.com/articles/harness-engineering.html) | Industry-elder ratification of the term. |
| [The Anatomy of an Agent Harness — LangChain](https://www.langchain.com/blog/the-anatomy-of-an-agent-harness) | Concrete breakdown of harness components. |
| [Effective context engineering for AI agents — Anthropic](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) | The "context engineering" → harness engineering bridge. |
| [Harness engineering: structured workflows — Red Hat](https://developers.redhat.com/articles/2026/04/07/harness-engineering-structured-workflows-ai-assisted-development) | Enterprise-side validation of the concept. |

## CLAUDE.md / AGENTS.md best practice

| Source | Why it matters |
|---|---|
| [AGENTS.md spec](https://agents.md/) | The emerging cross-tool standard, now under Linux Foundation stewardship. Used by 60,000+ open-source projects. |
| [How to write a great AGENTS.md — GitHub blog](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/) | GitHub's empirical study of 2,500 repos. Tells you what *actually* works in the wild. |
| [Writing a good CLAUDE.md — HumanLayer Blog](https://www.humanlayer.dev/blog/writing-a-good-claude-md) | Practical guidance: keep it < 300 lines, progressive disclosure, treat it like code. |
| [Karpathy's CLAUDE.md template](https://github.com/forrestchang/andrej-karpathy-skills) | 124k stars. The viral baseline; reference-grade behavioral rules. |
| [Best practices for Claude Code — Anthropic docs](https://code.claude.com/docs/en/best-practices) | Official guidance. |

## Self-improving agent harnesses

| Source | Why it matters |
|---|---|
| [ACE paper (arxiv 2510.04618)](https://arxiv.org/abs/2510.04618) | Structured-bullet + delta-edit pattern for evolving rulebooks without full rewrites. Cited by `/review-harness` for the "promote lesson" pattern. |
| [Agent drift analysis](https://prassanna.io/blog/agent-drift/) | Why fully-autonomous self-maintenance fails. Cited for the "PR-level, human-approved" philosophy. |

## The vibe-coding postmortem (story material)

| Source | Use |
|---|---|
| [Karpathy's original "vibe coding" tweet (Feb 2025)](https://x.com/karpathy/status/1886192184808149383) | The phrase's origin. |
| [Vibe coding — Wikipedia](https://en.wikipedia.org/wiki/Vibe_coding) | Authoritative timeline + Word of the Year citation. |
| [Vibe coding is passé — The New Stack](https://thenewstack.io/vibe-coding-is-passe/) | Karpathy's own retraction (Q1 2026). |
| [96% of Developers Distrust AI Code](https://earezki.com/ai-news/2026-04-26-vibe-coding-just-failed-its-first-real-audit/) | The trust collapse data point. |
| [Vibe coding could cause catastrophic 'explosions'](https://thenewstack.io/vibe-coding-could-cause-catastrophic-explosions-in-2026/) | The 470-PR analysis + Challenger comparison. |
| [Vibe Coding Failures: 7 Real Apps That Broke in Production](https://getautonoma.com/blog/vibe-coding-failures) | Concrete incident catalog. |
| [The Vibe Coding Hangover Is Real](https://dev.to/paulthedev/the-vibe-coding-hangover-is-real-what-nobody-tells-you-about-ai-generated-code-in-production-399h) | CTO survey + sprint-capacity-drain data. |
| [2026: The Year of Technical Debt — Salesforce Ben](https://www.salesforceben.com/2026-predictions-its-the-year-of-technical-debt-thanks-to-vibe-coding/) | The dominant 2026 enterprise framing. |
| [Vibe Coding in Practice — arxiv 2512.11922](https://arxiv.org/abs/2512.11922) | The first academic treatment. |
| [Beyond Vibe Coding into Agentic Engineering — dev.to](https://dev.to/aws-builders/beyond-vibe-coding-into-agentic-engineering-5fef) | The "what's next" framing. |
| [Vibe coding and agentic engineering are getting closer than I'd like — Simon Willison](https://simonwillison.net/2026/May/6/vibe-coding-and-agentic-engineering/) | The dissenting voice (worth quoting for balance). |

## Adjacent ecosystems

| Source | Why it matters |
|---|---|
| [mattpocock/skills](https://github.com/mattpocock/skills) | 70k stars. Workflow-skill reference (grill / triage / tdd). Sibling not competitor. |
| [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) | 124k stars. Behavioral-rules reference. Single-file viral case study. |
| [ai-boost/awesome-harness-engineering](https://github.com/ai-boost/awesome-harness-engineering) | The category's awesome list. Submit a PR here once stable. |
| [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | Official plugin directory. Submit once mature. |
| [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) | 1000+ skill catalog. Submit once mature. |
