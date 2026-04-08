# Playbook: Long-Running Autonomous Build

> Use this playbook for multi-session builds that span hours or days — full product builds, major rewrites, or autonomous development cycles.

## Architecture

This playbook uses up to three agent roles:

- **Planner**: expands a brief prompt into a full product spec
- **Generator**: builds features one at a time, committing incrementally
- **Evaluator**: tests the running product against explicit criteria, fails weak output

For simpler long-running tasks, the Generator and Evaluator may be the same agent working in alternating phases.

## Phase 1: Planning

### Input
A 1-4 sentence product description from the user.

### Steps

1. Expand the prompt into a comprehensive product spec
2. Organize features into sprints or priority tiers
3. Define the technical stack and architecture
4. Create user stories with acceptance criteria
5. Identify opportunities for AI-powered features
6. Output: `docs/spec.md` committed to the repo

### Planning Principles

- Be ambitious about scope — describe the full product
- Stay high-level on implementation — don't over-specify technical details
- Focus on user-facing behavior and product completeness
- Include a visual design direction

## Phase 2: Environment Setup

### Steps

1. Create the repo scaffold with standard structure
2. Write `AGENTS.md` using the [template](../templates/AGENTS.md.template)
3. Create `progress-tracker.json` from [template](../templates/progress-tracker.json) with all features from the spec
4. Write `init.sh` — starts the dev server and runs a basic health check
5. Initial git commit with the scaffold

### Critical Files

```
project/
├── AGENTS.md
├── init.sh
├── progress-tracker.json
├── docs/
│   ├── spec.md
│   └── architecture.md
└── [project scaffold]
```

## Phase 3: Build Loop

Each session follows this cycle:

### Session Start

```
1. Read AGENTS.md
2. Read progress-tracker.json
3. Check git log --oneline -20
4. Run init.sh to start the dev server
5. Run a basic smoke test to verify the app works
6. Choose the highest-priority incomplete feature
```

### Session Work

```
1. Implement one feature completely
2. Test it end-to-end in the running application
3. Fix any issues found
4. Commit: git commit -m "feat: [feature description]"
5. Update progress-tracker.json
6. If context is getting bloated, write a handoff artifact and exit
7. Otherwise, move to the next feature
```

### Session End

```
1. Ensure git is clean (no uncommitted changes)
2. Update progress-tracker.json with current status
3. If ending the session, write a handoff artifact
```

### Handoff Artifact

When ending a session (by choice or because context is degrading), write a handoff artifact. See [templates/handoff-artifact.md](../templates/handoff-artifact.md).

## Phase 4: Evaluation

### When To Evaluate

- After every 3-5 features (sprint boundary)
- When the build reaches a major milestone
- At the end of the full build

### Evaluation Process

1. Start the application
2. Navigate every major user flow
3. Score against the [evaluator rubric](../templates/evaluator-rubric.md)
4. Document bugs with reproduction steps
5. For each criterion below threshold: fail and return to generator

### Evaluation Criteria Categories

| Category | Weight | Description |
|----------|--------|-------------|
| Feature completeness | High | Are all spec'd features actually implemented and working? |
| Functionality | High | Can users complete core workflows without errors? |
| Design quality | Medium | Does it feel cohesive, not a collection of parts? |
| Code quality | Medium | Clean architecture, no dead code, proper error handling |
| Originality | Low | Deliberate choices vs. generic defaults |

### Common Evaluator Findings

- Features that exist in the UI but don't actually work
- Click handlers that do nothing
- API endpoints that return stubs
- Layout issues invisible from code review
- Broken navigation flows
- Race conditions on user input

## Phase 5: Context Management

### Signs Context Is Degrading

- Agent starts wrapping up prematurely ("I think we've made great progress...")
- Responses become repetitive
- Agent forgets constraints established earlier
- Quality drops noticeably
- Agent starts praising its own work without evidence

### Context Reset Protocol

1. Stop current work at a clean boundary
2. Commit all changes
3. Write a comprehensive handoff artifact
4. End the session
5. New session starts fresh from Phase 3 (Session Start)

### Compaction vs Reset

| Factor | Use Compaction | Use Reset |
|--------|---------------|-----------|
| Session length | < 2 hours | > 2 hours |
| Coherence | Still good | Degrading |
| Context anxiety | Not present | Present |
| Task complexity | Moderate | High |

## Iteration

### When The Evaluator Fails A Sprint

1. Read the evaluator's feedback carefully
2. Prioritize bugs by severity
3. Fix all blocking bugs
4. Re-run evaluation
5. Only proceed to next sprint when current sprint passes

### Simplifying The Harness Over Time

As models improve, re-evaluate whether each harness component is still load-bearing:

- Can the model handle larger chunks without sprint decomposition?
- Does it still need explicit evaluation, or does self-evaluation work for this task?
- Are context resets still needed, or does compaction suffice?

Strip away pieces that are no longer necessary. Add new pieces for capabilities that weren't possible before.

## Cost and Duration Reference

Based on published experiments:

| Harness Type | Duration | Relative Cost |
|-------------|----------|---------------|
| Single agent, no harness | ~20 min | Low |
| Full harness (Planner + Generator + Evaluator) | 4-6 hours | 15-25x single agent |
| Simplified harness (newer models) | 2-4 hours | 10-15x single agent |

The harness costs more but produces dramatically higher quality — especially for complex products where the single-agent approach tends to produce broken core features.
