# Roadmap

The bet: a small, sharp toolkit beats a sprawling one. Four skills cover the harness lifecycle; a fifth, `/test-first`, drives the inner build loop. Adding more requires evidence that vibe-coding pain isn't covered.

## v0.1 — Lifecycle complete (current)

| Stage | Skill | Pain treated |
|---|---|---|
| Create | `/bootstrap-harness` | "My repo has no AI rulebook" |
| Build | `/test-first` | "The agent's code outran its tests" |
| Use daily | `/finish-phase` | "We never close out shipped work" |
| Audit | `/review-harness` | "The docs have drifted from the code" |
| Evolve | `/promote-lesson` | "We keep hitting the same mistake" |

Four of them maintain the harness's *rules and context* (the docs system); `/test-first` drives its tightest *feedback loop* — the red-green cycle. Together they implement what OpenAI calls *harness engineering*: rules, context, and feedback loops that make agents reliable, not just smarter prompts.

## v0.2 candidates (not committed)

Listed for transparency, not promised. Each must clear the bar: *"What vibe-coding-era pain does this fix that the existing four don't?"*

| Candidate | Proposed pain | Why not yet |
|---|---|---|
| `/migrate-to-agents-md` | "My CLAUDE.md needs to become an AGENTS.md (or both)" | Edge case until cross-tool teams are the majority |
| `/diagnose-drift` | "An agent just did something wrong — which harness rule failed?" | Probably belongs inside `/review-harness` rather than as a new skill |
| `/onboard-agent` | "I'm starting a new session — what should the agent read first?" | `docs/exec-plans/README.md` (created by `/bootstrap-harness`) already does this |

## Principles for adding skills

1. **One pain per skill** — if a new skill's pitch overlaps with an existing one by more than 30%, it shouldn't be its own skill
2. **Cite the research** — each skill links to the paper or study that justifies its existence
3. **No emoji-named skills** — boundaries (`✅` / `⚠️` / `🚫`) are the only place emoji carries meaning
4. **Small over comprehensive** — Karpathy got 124k stars with one file; Pocock got 70k with thirty. Quality of fit beats quantity of coverage.

## What's intentionally NOT here

- **General code-generation skills** — open-ended "build me X" belongs to the rest of the ecosystem (Karpathy's behavioral rules, framework scaffolders). The one build-loop skill we *do* ship is `/test-first`, because TDD's red-green cycle is a harness *feedback loop* — it constrains agent output, it doesn't just emit it.
- **CI / GitHub Action wrappers** — the harness is a docs system. CI integration belongs to a separate plugin if anyone wants it.
- **Per-language skills** (`/bootstrap-harness-rust`, etc.) — `/bootstrap-harness` already adapts to the detected stack. Per-language forks would fragment the ecosystem.
