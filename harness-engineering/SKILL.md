---
name: harness-engineering
description: Use when starting work in a new or unfamiliar repository, or when designing or improving project instruction files, repository docs, planning artifacts, evaluation loops, observability, or agent workflows.
---

# Harness Engineering

## Overview

Design the repository and runtime around the agent, not the other way around.

This skill turns two recurring ideas into operating rules:

- OpenAI: humans should steer while agents execute, and the repository should be the agent's durable knowledge base.
- Anthropic: long-running agent work improves when planning, generation, and evaluation are separated, with structured handoffs and real-world verification.

Use this skill in two modes:

1. **Default startup mode** for any new or unfamiliar repository
2. **Focused redesign mode** when the task explicitly involves project instructions, docs, plans, QA, or long-running autonomy

If the repository already works well for agents, keep the harness light. If the agent is drifting, guessing, or self-approving weak work, strengthen the harness.

## Core Principles

### 1. The repository is the system of record

If critical knowledge only lives in chat, Slack, tickets, or human memory, the agent does not reliably have it.

Repository knowledge should be:

- versioned
- linked
- reviewable
- mechanically checkable
- close to the code it governs

Treat `docs/` as the durable knowledge base and keep the project's entrypoint instructions short enough to serve as a map.

### 2. The project instruction file is a map, not an encyclopedia

Do not build one giant instruction blob.

Whether your client uses `AGENTS.md`, `CLAUDE.md`, or another project instruction file, keep it brief, stable, and navigational:

- what this repo is
- how work is organized
- where deeper docs live
- how to verify work
- what not to change casually

If multiple instruction files exist, keep one source of truth and make the others point to it.

### 3. Separate planning, doing, and judging

Do not rely on one agent to spec the work, implement it, and grade itself.

For simple tasks, one agent may play multiple roles sequentially. For long-running, ambiguous, or subjective tasks, separate the roles explicitly:

- `Planner`: expands intent into a usable spec
- `Generator`: builds the thing
- `Evaluator`: tests, critiques, and fails weak output

### 4. Make quality gradable

If the instruction is "make it better," the result will usually be generic.

Convert vague goals into criteria that can be checked. Criteria should be:

- concrete
- task-specific
- weighted by importance
- strict enough to reject mediocre work

Define criteria first, then let the evaluator score against them.

### 5. Verify against reality, not just code

The best harnesses verify behavior in the running system:

- use tests for correctness
- use browser automation for UI behavior
- inspect logs, metrics, traces, and database state when relevant
- verify user workflows end to end

An evaluator that only reads code misses too much.

### 6. Use structured handoffs for long-running work

Long tasks degrade when context gets bloated or incoherent.

When runs are lengthy, create explicit handoff artifacts:

- current state
- decisions made
- known failures
- next actions
- verification status

When coherence falls, prefer a clean reset plus a strong handoff artifact over letting a bloated session limp onward.

### 7. Add complexity only when it earns its keep

Every harness component is a claim that the model cannot yet do something reliably.

Stress-test those claims. Keep the simplest harness that closes the actual failure mode.

## When To Use

Use this skill immediately when:

- entering a new repository
- the repository has weak or missing project instructions, docs, or plans
- tasks are long-running, multi-step, or likely to span sessions
- the work is subjective or hard to self-grade
- agents keep drifting, redoing work, or missing constraints
- agents claim success without meaningful QA
- the team wants to make the repo broadly agent-friendly, not just solve one task

Do not skip this skill just because the task seems small. A short task in a repo with hidden knowledge can still fail for harness reasons.

## The Startup Pass

At the start of work in any new or unfamiliar repository, perform a fast harness audit before major implementation.

Check for these layers:

- a project instruction file such as `AGENTS.md`, `CLAUDE.md`, or equivalent
- architecture or domain map
- versioned product/spec documentation
- implementation plans or decision logs
- verification commands and test entry points
- runtime bootstrap surface: startup scripts, environment summaries, toolchain discovery
- CI or mechanical enforcement
- observability surface: logs, metrics, traces, screenshots, fixtures
- quality or debt tracking

Classify the repo:

- **Ready**: agent can navigate, change, and verify safely
- **Usable but fragile**: work can proceed, but missing structure will cause rework
- **Not agent-ready**: critical knowledge is implicit or unverifiable

If the repo is not agent-ready, propose harness work before large feature work.

## Minimum Viable Harness

For most repositories, establish at least this baseline:

### Entry point

- a project instruction file
- keep it short: usually 50 to 150 lines
- include repo purpose, commands, doc map, constraints, and verification entry points

### System of record

- `docs/` as durable knowledge base
- prefer small, linked documents over one monolith
- store decisions where agents can read them later

### Architecture map

- top-level domain overview
- ownership or boundaries
- key data flows
- major dependencies

### Planning artifacts

- lightweight plan for small work
- richer execution plan for larger work
- progress log and decision log for long-running tasks

### Verification surface

- exact commands for tests, lint, build, and local runs
- browser or API validation path for user-facing features
- known acceptance criteria

### Runtime bootstrap

- a bounded environment snapshot when the runtime is non-obvious
- enough startup context to avoid wasting early turns on `pwd`, `ls`, and version checks
- cheap collection: one script or command, hard timeouts, truncation, and safe fallback on failure

### Quality tracking

- explicit list of gaps, debt, and flaky areas
- a place to record recurring evaluator findings

## Designing The Project Instruction File

The project instruction file should answer only the first questions an agent needs:

1. What is this repository?
2. Where are the real source documents?
3. How should changes be made here?
4. How is correctness checked?
5. What areas are risky, frozen, or easy to break?

Avoid:

- giant policy dumps
- duplicated documentation
- stale examples
- detailed implementation facts that belong in deeper docs

If you cannot keep the instruction file short, the problem is usually missing structure elsewhere.

## Designing Evaluators

Use an evaluator when any of these are true:

- the task is long-running
- quality is subjective
- the output can look impressive while still being broken
- the generator tends to approve shallow work
- acceptance depends on real workflows, not just static code

An evaluator should:

- inspect the running product, not just source files
- compare output against explicit criteria or a sprint contract
- produce concrete failures, not vibes
- be skeptical by default
- block completion when thresholds are not met

Useful evaluator outputs:

- scorecard by criterion
- bug list with reproduction steps
- contract pass/fail results
- next-iteration guidance

## Runtime Bootstrap And Completion Gates

Use runtime bootstrap only when it closes a real failure mode, such as agents repeatedly spending their first turns rediscovering the environment.

Good bootstrap data is:

- compact: working directory, key files or directories, available runtimes, package managers, and relevant resource limits
- bounded: collected with one script or command, short timeouts, and truncation for large outputs
- fail-soft: if collection fails, the agent should continue normally rather than crash

Add completion gates before claiming success:

- restate the minimum files that were required to change
- check for unintended side effects or leftover artifacts
- verify from at least the implementation, QA, and user-outcome perspectives
- block "done" until there is concrete verification evidence

If a blind spot keeps recurring, prefer a narrow helper script, hook, or tool over a larger prompt.

## Sprint Contracts And Acceptance Contracts

When the product spec is high-level, bridge the gap with a contract before implementation.

For each chunk of work, define:

- what will be built
- what will not be built
- how success will be tested
- what artifacts prove completion

Let the builder propose the contract, let the evaluator challenge it, and iterate until "done" is concrete enough to test.

## Observability For Agents

Make the app legible to agents.

Good harnesses expose:

- human- and machine-readable logs
- stable local run commands
- browser snapshots or screenshots
- test fixtures and seed data
- metrics and traces where relevant
- clear error surfaces instead of silent failures

If the application cannot be observed by the agent, the agent cannot reliably improve it.

## Mechanical Enforcement

Do not rely on memory or goodwill alone.

Where possible, add cheap enforcement:

- CI checks for docs and links that must exist
- lint rules for architectural boundaries
- scripts that validate generated references or indexes
- recurring cleanup for stale docs and obsolete plans

## Anti-Patterns

Avoid these failure modes:

- one giant project instruction file
- important knowledge living outside the repo
- hidden acceptance criteria
- agents grading their own work without an external check
- plans that are not committed or not updated
- no artifact for long-running handoff
- trusting polished UI over tested behavior
- adding harness complexity because it sounds advanced
- keeping stale docs that disagree with the code

## Related Workflows

This skill is the environment-design layer.

Pair it with planning, TDD, debugging, verification, and browser automation workflows when those are available in your client or team setup.

## Deliverables

When this skill triggers for real repository work, aim to leave behind some combination of:

- a concise project instruction file
- a doc index or architecture map
- a versioned plan or spec
- explicit verification commands
- a bounded bootstrap script or hook when environment discovery is costly
- an evaluator rubric or acceptance contract
- a visible debt or quality tracker
- a handoff artifact for long-running work

Do not force all of them on every repository. Build the minimum harness that makes the next tranche of work reliable.

## References

- OpenAI, "Harness engineering: leveraging Codex in an agent-first world"  
  https://openai.com/index/harness-engineering/
- Anthropic, "Harness design for long-running application development"  
  https://www.anthropic.com/engineering/harness-design-long-running-apps
