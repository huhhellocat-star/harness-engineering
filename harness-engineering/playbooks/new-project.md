# Playbook: New Project Kickoff

> Use this playbook when starting a project from scratch — greenfield product, new service, or new repository.

## Phase 1: Expand Intent Into Spec

Before writing any code, expand the initial idea into a structured product spec.

### Steps

1. **Receive the prompt** — usually 1-4 sentences describing the product
2. **Expand into a full spec** with:
   - Product overview and target users
   - Feature list organized by priority (must-have, should-have, nice-to-have)
   - User stories with acceptance criteria for each feature
   - Technical architecture decisions (stack, database, API design)
   - Visual design direction (if applicable)
3. **Be ambitious about scope** — the spec should describe the full product, not a minimal demo
4. **Stay high-level on implementation** — specify what to build, not how to build it. Granular technical details specified upfront tend to cascade errors downstream.
5. **Look for opportunities to integrate AI features** into the product itself

### Spec Template

```markdown
# [Product Name]

## Overview
[2-3 sentences describing what this is and who it's for]

## Technical Stack
- Frontend: [framework]
- Backend: [framework]
- Database: [choice]
- Key dependencies: [list]

## Features

### Sprint 1: Foundation
- [ ] Feature 1: [description]
  - User story: As a [user], I want to [action] so that [benefit]
  - Acceptance: [concrete test]
- [ ] Feature 2: ...

### Sprint 2: Core
...

### Sprint 3: Polish
...

## Design Direction
[Visual style, color palette, typography, layout philosophy]
```

## Phase 2: Set Up The Harness

Before coding, establish the minimum viable harness.

### Steps

1. **Initialize the repository** with standard structure
2. **Create the instruction file** (`AGENTS.md` or `CLAUDE.md`) using the [template](../templates/AGENTS.md.template)
3. **Create a feature tracker** using [progress-tracker.json](../templates/progress-tracker.json)
4. **Set up the development environment** — install dependencies, create startup script
5. **Create an initial git commit** with the scaffold
6. **Write an `init.sh`** script that starts the dev server and runs basic health checks

### Harness Files To Create

```
project/
├── AGENTS.md (or CLAUDE.md)
├── docs/
│   ├── architecture.md
│   └── spec.md
├── init.sh
├── progress-tracker.json
└── [standard project files]
```

## Phase 3: Build Incrementally

Work on one feature at a time, in priority order.

### Per-Feature Loop

1. Read the progress tracker — choose the highest-priority incomplete feature
2. Implement the feature fully
3. Test it end-to-end (run the app, use browser automation if applicable)
4. Commit with a descriptive message: `feat: [feature name] — [what it does]`
5. Update the progress tracker (mark as passing, add notes)
6. Move to the next feature

### Rules

- Never try to implement multiple features at once
- Never mark a feature as done without testing it in the running application
- If a feature breaks a previous feature, fix the regression before moving on
- Commit after every feature, not after every file change

## Phase 4: Evaluate

After completing a milestone (every 3-5 features, or at the end of a sprint):

1. Run all tests
2. Navigate the full application as a user would
3. Score against the evaluator criteria (see [evaluator rubric](../templates/evaluator-rubric.md))
4. Fix any issues that fall below threshold
5. Write a progress summary

## Common Failure Modes

| Failure | Mitigation |
|---------|------------|
| Trying to build everything at once | Strict one-feature-at-a-time discipline |
| Declaring victory without testing | End-to-end verification before marking done |
| Losing coherence as context fills | Context reset with handoff artifact |
| Under-scoping the spec | Use a planner agent to expand the initial prompt |
| Stubbing features instead of implementing | Evaluator catches stubs and fails them |
