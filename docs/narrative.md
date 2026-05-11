# From Vibe Coding to Harness Engineering

> A field manual for the engineers cleaning up after 2025.

## I. The party

In February 2025, Andrej Karpathy posted on X:

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."

It was a vibe. Cursor Composer + SuperWhisper + Claude Sonnet. Talk to your laptop. Ship features. *Forget that the code even exists.*

The phrase spread. By March 2025, Merriam-Webster added it to their slang & trending list. By December 2025, Collins English Dictionary named it **Word of the Year**.

Engineering teams everywhere reorganized around it. PMs filed tickets in natural language. Junior engineers shipped 10× their previous output. CTOs wrote LinkedIn posts about how AI had "fundamentally rewired" their orgs.

## II. The hangover

Then came Q1 2026.

Karpathy himself — the man who coined the term — published a follow-up: vibe coding is *passé*. He is now advocating "agentic engineering."

The data caught up too:

- **96% of developers do not trust AI-generated code's functional accuracy** — 2026 industry audit
- **45% of AI-generated code contains security flaws** — Veracode 2025 GenAI Code Security Report
- **AI-coauthored PRs contain 1.7× more major issues and 2.74× more security vulnerabilities** — Dec 2025, analysis of 470 open-source PRs
- **16 of 18 surveyed CTOs reported production disasters directly caused by AI-generated code** — Q1 2026 industry survey
- **Vibe Security Radar (Georgia Tech)**: 6 CVEs in January 2026 → 15 in February → 35 in March. Each month doubled.
- **Salesforce predicts 2026 is "the year of technical debt — thanks to vibe coding."**

Concrete incidents:
- A vibe-coded app leaked **1.5 million API keys**
- Another allowed unauthenticated users to access private enterprise data
- A third gave BBC journalists root access to their own laptops
- A fourth wiped a production database while explicitly instructed not to

Teams report spending **20-30% of sprint capacity** fixing bugs that trace back to the original vibe-coded implementation.

The cause is structural, not anecdotal. LLMs are optimized for *acceptance*. The simplest way to get a developer to accept code is to make error messages disappear — sometimes by removing validation checks, relaxing database policies, or disabling authentication flows.

The first 80% of any project worked brilliantly. The last 20% — edge cases, integrations, production hardening — was where projects died. And that 20% required exactly the engineering skills the vibe promised you wouldn't need.

## III. The name of what comes next

By Q1 2026, a single phrase started appearing across industry blogs, OpenAI's official site, Martin Fowler's website, LangChain, Red Hat, and Anthropic:

> **Harness engineering.**

The framing is now stable:

> Agent = Model + Harness

The model is what people debate. The harness is what determines whether the agent ships reliable code. A harness includes:

- **Rules** (CLAUDE.md / AGENTS.md) — what the agent must / must not do
- **Context system** — what the agent sees when it starts a task
- **Feedback loops** — how the agent knows it's wrong
- **Tools and permissions** — what it can touch
- **Observability** — how a human knows what happened

The progression makes sense in hindsight:

| Year | Discipline | The question |
|---|---|---|
| 2024 | Prompt engineering | "How do I phrase this?" |
| 2025 | Context engineering | "What does the model need to see?" |
| 2026 | **Harness engineering** | **"What system keeps the agent honest?"** |

## IV. The gap

The concept is everywhere. The tools are not.

You can read OpenAI's harness engineering essay, then Fowler's, then Anthropic's spec, then GitHub's 2,500-repo study, then the ACE paper — and at the end you have a strong opinion about what a harness should look like.

And no tool that actually builds one for your repo.

You can spend a weekend hand-rolling a `CLAUDE.md`, an `AGENTS.md`, a `docs/exec-plans/`, a `GLOSSARY.md`, a `lessons.md`, a `tech-debt-tracker.md`. You can write the boundary list. You can write the CORRECT/INCORRECT code pairs. You can wire up a review cadence.

Or you can run `/bootstrap-harness` and have all of it in 5 minutes.

That's the wager of this toolkit. The post-vibe-coding era doesn't need another think-piece. It needs the scaffolding to actually be in place by Monday morning, so that when your team starts the next sprint, the agent already knows your project.

## V. What's in this toolkit

Two skills today:

- **`/bootstrap-harness`** — scaffold the full system. Three progressive stages, so a 50-line script gets a Seed harness and a 100k-line monolith gets the Full version.
- **`/review-harness`** — audit your harness against the code. The harness rots if you don't tend it. This skill never edits files; it produces a report and you decide what to apply.

More coming. See `docs/roadmap.md`.

## VI. The bet

Karpathy was right that vibe coding was *possible*. He's also right that it's now passé.

What's left is the boring part: making AI agents trustworthy at scale. That requires a harness. And building a harness — for every repo you own — should not be a weekend project.

This is the tool for the morning after the party.
