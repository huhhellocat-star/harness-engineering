---
name: harness-engineering
description: Use when starting work in a new or unfamiliar repository, when designing or improving project instruction files, repository docs, planning artifacts, evaluation loops, observability, or agent workflows, or when performing any product development or project modification task that benefits from structured engineering practices.
---

# Harness Engineering

## Overview

Design the repository and runtime around the agent, not the other way around.

**Harness engineering** is the practice of shaping the environment, constraints, and feedback loops around AI coding agents so they work reliably. The engineer's output shifts from code to a constraint system: instruction files, architecture rules, linters, evaluation criteria, and structured handoffs.

```
Traditional:  Human writes code → Machine executes code
Harness:      Human designs constraints → Agent writes code → Machine executes code
```

This skill synthesizes three bodies of work into operating rules:

- **OpenAI**: humans steer while agents execute; the repository is the agent's durable knowledge base; mechanical enforcement over prose rules; entropy must be actively managed.
- **Anthropic**: long-running agent work improves when planning, generation, and evaluation are separated; structured handoffs and context resets beat bloated sessions; verify against the running system.
- **Community (Ralph pattern)**: fresh context is reliability; backpressure over prescription; disk is state, git is memory; steer with signals, not scripts.

## How To Use This Skill

This skill operates in three modes:

1. **Startup audit** — fast harness check when entering any new or unfamiliar repository
2. **Workflow mode** — follow a specific playbook for the task at hand
3. **Redesign mode** — focused improvement of the repo's agent-readiness

### Workflow Router

Choose the playbook that matches the current task:

| Situation | Playbook |
|-----------|----------|
| Starting a new project from scratch | [playbooks/new-project.md](playbooks/new-project.md) |
| Building a feature in an existing repo | [playbooks/feature-development.md](playbooks/feature-development.md) |
| Multi-session autonomous build (hours/days) | [playbooks/long-running-build.md](playbooks/long-running-build.md) |
| Refactoring, cleanup, or debt reduction | [playbooks/refactor-cleanup.md](playbooks/refactor-cleanup.md) |
| Investigating and fixing a bug | [playbooks/bugfix-investigation.md](playbooks/bugfix-investigation.md) |

If the repository already works well for agents, keep the harness light. If the agent is drifting, guessing, or self-approving weak work, strengthen the harness.

## Core Principles

### 1. The repository is the system of record

If critical knowledge only lives in chat, Slack, tickets, or human memory, the agent does not reliably have it.

Repository knowledge should be versioned, linked, reviewable, mechanically checkable, and close to the code it governs.

Treat `docs/` as the durable knowledge base. Keep the project's entrypoint instructions short enough to serve as a map. Everything in the repo is a signal the agent will learn from — including bad patterns.

### 2. The project instruction file is a map, not an encyclopedia

Do not build one giant instruction blob. Keep it under ~150 lines.

Whether your client uses `AGENTS.md`, `CLAUDE.md`, or another instruction file, keep it brief, stable, and navigational:

- what this repo is
- how work is organized
- where deeper docs live
- how to verify work
- what not to change casually

If you cannot keep the instruction file short, the problem is usually missing structure elsewhere. See [templates/AGENTS.md.template](templates/AGENTS.md.template) for a ready-to-use starting point.

### 3. Separate planning, doing, and judging

Do not rely on one agent to spec the work, implement it, and grade itself. Agents reliably skew positive when grading their own work — even when quality is obviously mediocre.

For simple tasks, one agent may play multiple roles sequentially. For long-running, ambiguous, or subjective tasks, separate the roles explicitly:

- **Planner**: expands a short intent into a full spec with user stories and acceptance criteria
- **Generator**: builds the thing, one feature at a time, committing progress incrementally
- **Evaluator**: tests against the running product, critiques against explicit criteria, and fails weak output

Tuning a standalone evaluator to be skeptical is far more tractable than making a generator critical of its own work.

### 4. Make quality gradable

If the instruction is "make it better," the result will usually be generic.

Convert vague goals into criteria that can be checked. Criteria should be concrete, task-specific, weighted by importance, and strict enough to reject mediocre work. Good criteria categories:

- **Functionality**: does it work end-to-end as a user would expect?
- **Design quality**: does it feel like a coherent whole, not a collection of parts?
- **Originality**: are there deliberate creative choices, or is this all defaults?
- **Craft**: spacing, typography, color harmony, contrast — the competence check
- **Code quality**: clean architecture, no dead code, proper error handling

Define criteria first, then let the evaluator score against them. See [templates/evaluator-rubric.md](templates/evaluator-rubric.md).

### 5. Verify against reality, not just code

The best harnesses verify behavior in the running system:

- use tests for correctness
- use browser automation (Playwright, Puppeteer) for UI behavior
- inspect logs, metrics, traces, and database state when relevant
- verify user workflows end to end
- have the evaluator actually navigate and interact with the product

An evaluator that only reads code misses too much. An evaluator that reads screenshots catches layout issues. An evaluator that clicks through the app catches broken flows.

### 6. Use structured handoffs for long-running work

Long tasks degrade when context gets bloated or incoherent. Models can exhibit "context anxiety" — wrapping up work prematurely as context fills.

When runs are lengthy, prefer **context resets with structured handoff artifacts** over compaction alone:

- current state of the build
- decisions made and rationale
- known failures and workarounds
- next actions with priority
- verification status per feature

A clean reset plus a strong handoff artifact beats a bloated session limping onward. See [templates/handoff-artifact.md](templates/handoff-artifact.md).

### 7. Work incrementally, commit often

Do not try to build everything at once. Work on one feature at a time:

- pick the highest-priority incomplete feature
- implement it fully
- test it end-to-end
- commit with a descriptive message
- update progress tracking
- then move to the next feature

This prevents half-implemented features, makes rollback easy, and gives the next session clear state. Use git as memory — descriptive commits are the handoff mechanism.

### 8. Manage entropy actively

Agents reproduce whatever patterns exist in the repo — including bad ones. Technical debt compounds faster with agents because they amplify existing patterns at scale.

- Encode "golden rules" as lint rules and CI checks, not just docs
- Run periodic quality audits to detect pattern drift
- Fix bad patterns early — they propagate exponentially
- Track debt explicitly so agents don't perpetuate it

### 9. Add complexity only when it earns its keep

Every harness component is a claim that the model cannot yet do something reliably.

Stress-test those claims. When a new model lands, re-examine the harness — strip away pieces that are no longer load-bearing and add new pieces for capabilities that weren't possible before. The interesting harness combinations don't shrink as models improve; they move.

## When To Use

Use this skill immediately when:

- entering a new or unfamiliar repository
- the repository has weak or missing project instructions, docs, or plans
- starting product development or significant project modifications
- tasks are long-running, multi-step, or likely to span sessions
- the work is subjective or hard to self-grade
- agents keep drifting, redoing work, or missing constraints
- agents claim success without meaningful QA
- the team wants to make the repo broadly agent-friendly

Do not skip this skill just because the task seems small. A short task in a repo with hidden knowledge can still fail for harness reasons.

## The Startup Pass

At the start of work in any new or unfamiliar repository, perform a fast harness audit before major implementation.

### Checklist

- [ ] Project instruction file (`AGENTS.md`, `CLAUDE.md`, or equivalent)
- [ ] Architecture or domain map
- [ ] Versioned product/spec documentation
- [ ] Implementation plans or decision logs
- [ ] Verification commands and test entry points
- [ ] Runtime bootstrap: startup scripts, environment summaries, toolchain discovery
- [ ] CI or mechanical enforcement
- [ ] Observability surface: logs, metrics, traces, screenshots, fixtures
- [ ] Quality or debt tracking

### Classification

- **Ready**: agent can navigate, change, and verify safely
- **Usable but fragile**: work can proceed, but missing structure will cause rework
- **Not agent-ready**: critical knowledge is implicit or unverifiable

If the repo is not agent-ready, propose harness work before large feature work. The minimum viable harness comes first.

## Minimum Viable Harness

For most repositories, establish at least this baseline:

### Entry point

- a project instruction file (see [templates/AGENTS.md.template](templates/AGENTS.md.template))
- keep it short: 50 to 150 lines
- include repo purpose, commands, doc map, constraints, and verification entry points

### System of record

- `docs/` as durable knowledge base
- prefer small, linked documents over one monolith
- store decisions where agents can read them later

### Architecture map

- top-level domain overview
- ownership or boundaries
- key data flows and major dependencies

### Planning artifacts

- lightweight plan for small work
- richer execution plan with feature list for larger work
- progress log and decision log for long-running tasks
- see [templates/progress-tracker.json](templates/progress-tracker.json) for structured feature tracking

### Verification surface

- exact commands for tests, lint, build, and local runs
- browser or API validation path for user-facing features
- known acceptance criteria

### Runtime bootstrap

- bounded environment snapshot when the runtime is non-obvious
- enough startup context to avoid wasting early turns on discovery
- cheap collection: one script or command, hard timeouts, truncation, and safe fallback on failure

### Quality tracking

- explicit list of gaps, debt, and flaky areas
- a place to record recurring evaluator findings

## Context Engineering

Effective context management is critical for agent performance.

### Progressive disclosure

Organize knowledge so agents discover it incrementally:

1. Instruction file → top-level map (~100 lines)
2. Directory-level docs → scope and conventions per module
3. Inline comments → only for non-obvious logic

Each layer points to the next. Never dump everything into one file.

### Context resets vs compaction

- **Compaction**: summarize earlier context, keep the same session. Good for medium tasks.
- **Context reset**: end the session, write a handoff artifact, start fresh. Better for long tasks where coherence degrades or context anxiety appears.

Signs you need a context reset:
- the agent starts wrapping up prematurely
- responses become repetitive or unfocused
- the agent forgets constraints established earlier
- quality drops noticeably in later features

### Fresh context reliability

Each new session should:
1. Read the instruction file and progress tracker
2. Check git log for recent work
3. Run a basic health check on the development server
4. Choose the next feature to implement
5. Begin work with full focus

This is the "Ralph Wiggum loop" — every iteration starts fresh, reads the state from disk, does one unit of work, commits, and exits cleanly.

## Multi-Agent Patterns

### When to use multi-agent

| Task complexity | Recommended pattern |
|----------------|-------------------|
| Simple fix or small feature | Single agent, sequential roles |
| Medium feature (1-2 hours) | Single agent with self-evaluation pause |
| Large feature or full product | Planner → Generator → Evaluator |
| Subjective quality (design, UX) | Generator ↔ Evaluator feedback loop |
| Multi-day autonomous build | Full harness with sprint contracts |

### Sprint contracts

When the product spec is high-level, bridge the gap with a contract before implementation.

For each chunk of work, define:

- what will be built (scope)
- what will not be built (boundaries)
- how success will be tested (acceptance criteria)
- what artifacts prove completion (evidence)

Let the builder propose the contract, let the evaluator challenge it, and iterate until "done" is concrete enough to test. See [templates/sprint-contract.md](templates/sprint-contract.md).

### Evaluator design

Use an evaluator when:

- the task is long-running
- quality is subjective
- the output can look impressive while still being broken
- acceptance depends on real workflows, not just static code

An effective evaluator:

- inspects the running product, not just source files
- compares output against explicit criteria or a sprint contract
- produces concrete failures with reproduction steps
- is skeptical by default — tuned to reject, not to praise
- blocks completion when thresholds are not met

See [templates/evaluator-rubric.md](templates/evaluator-rubric.md) for a ready-to-use rubric.

## Mechanical Enforcement

Do not rely on memory or goodwill alone.

Where possible, add cheap enforcement:

- CI checks for docs and links that must exist
- lint rules for architectural boundaries (custom linters with embedded fix instructions)
- scripts that validate generated references or indexes
- recurring cleanup for stale docs and obsolete plans
- structural tests that verify module boundaries

Lint error messages should include fix instructions — agents can self-correct from actionable error messages. Enforce centrally, allow autonomy locally.

## Observability For Agents

Make the app legible to agents:

- human- and machine-readable logs
- stable local run commands
- browser snapshots or screenshots
- test fixtures and seed data
- metrics and traces where relevant
- clear error surfaces instead of silent failures
- per-worktree observability when running parallel agents

If the application cannot be observed by the agent, the agent cannot reliably improve it.

## Anti-Patterns

Avoid these failure modes:

- one giant project instruction file (>300 lines)
- important knowledge living outside the repo
- hidden acceptance criteria
- agents grading their own work without an external check
- plans that are not committed or not updated
- no artifact for long-running handoff
- trusting polished UI over tested behavior
- adding harness complexity because it sounds advanced
- keeping stale docs that disagree with the code
- premature declaration of victory (marking features done without end-to-end testing)
- one-shotting large builds instead of working incrementally

## Templates

Ready-to-use templates for key artifacts:

| Template | Purpose |
|----------|---------|
| [templates/AGENTS.md.template](templates/AGENTS.md.template) | Project instruction file for any repo |
| [templates/handoff-artifact.md](templates/handoff-artifact.md) | Session handoff for long-running work |
| [templates/sprint-contract.md](templates/sprint-contract.md) | Sprint contract between builder and evaluator |
| [templates/evaluator-rubric.md](templates/evaluator-rubric.md) | Evaluator scoring criteria |
| [templates/progress-tracker.json](templates/progress-tracker.json) | Structured feature tracking in JSON |

## Deliverables

When this skill triggers for real repository work, aim to leave behind the minimum set from:

- a concise project instruction file
- a doc index or architecture map
- a versioned plan or spec
- explicit verification commands
- a bounded bootstrap script or hook when environment discovery is costly
- an evaluator rubric or acceptance contract
- a visible debt or quality tracker
- a handoff artifact for long-running work
- progress tracking in a structured format (JSON preferred over markdown for agent reliability)

Do not force all of them on every repository. Build the minimum harness that makes the next tranche of work reliable.

## References

For a comprehensive list of harness engineering resources, see [references/ecosystem.md](references/ecosystem.md).

Key sources:

- OpenAI, "Harness engineering: leveraging Codex in an agent-first world" — https://openai.com/index/harness-engineering/
- Anthropic, "Harness design for long-running application development" — https://www.anthropic.com/engineering/harness-design-long-running-apps
- Anthropic, "Effective harnesses for long-running coding agents" — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- Anthropic, "Building effective agents" — https://www.anthropic.com/research/building-effective-agents
- Ralph (snarktank/ralph) — The iterative agent loop pattern
- Awesome Harness Engineering (walkinglabs) — Community resource collection
