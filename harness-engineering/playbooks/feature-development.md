# Playbook: Feature Development

> Use this playbook when adding a feature to an existing, established repository.

## Phase 1: Orientation

Before touching any code, understand the landscape.

### Steps

1. **Read the instruction file** (`AGENTS.md`, `CLAUDE.md`, or equivalent)
2. **Scan the architecture** — understand the module boundaries, data flows, and key abstractions
3. **Read recent git history** — understand what changed recently and the team's conventions
4. **Identify the test suite** — know how to run tests and what coverage looks like
5. **Run the application** — verify it works before you change anything
6. **Locate related code** — find existing patterns for the type of feature you're building

### Key Questions

- Where does this feature fit in the existing architecture?
- What existing patterns should this feature follow?
- What tests already exist for related functionality?
- Are there any constraints or boundaries documented in the instruction file?
- What's the verification path — tests, browser automation, API calls?

## Phase 2: Plan The Change

### For Small Features (< 1 hour)

Create a mental plan:
- Which files will change
- What the new behavior is
- How to test it

### For Medium Features (1-4 hours)

Write a brief plan:

```markdown
## Feature: [name]

### What
[1-2 sentence description]

### Changes
- [ ] [file/module]: [what changes]
- [ ] [file/module]: [what changes]
- [ ] Tests: [what to add]

### Acceptance
- [ ] [concrete test 1]
- [ ] [concrete test 2]

### Risks
- [anything that might break]
```

### For Large Features (> 4 hours)

Use a sprint contract (see [templates/sprint-contract.md](../templates/sprint-contract.md)) and consider breaking into sub-features.

## Phase 3: Implement

### Rules

1. **Follow existing patterns** — match the codebase's style, naming, and structure
2. **Change the minimum necessary** — surgical precision, not sweeping rewrites
3. **Keep commits atomic** — one logical change per commit
4. **Write tests alongside code** — not after
5. **Run existing tests frequently** — catch regressions immediately

### Implementation Order

1. Data model changes (if any)
2. Backend logic
3. API endpoints
4. Frontend components
5. Integration wiring
6. Tests
7. Documentation updates

### Commit Messages

Use conventional commits:
```
feat: add user search with fuzzy matching
fix: prevent duplicate entries in cart
refactor: extract payment validation into shared module
test: add integration tests for notification service
docs: update API reference for search endpoint
```

## Phase 4: Verify

### Verification Checklist

- [ ] All existing tests pass
- [ ] New tests cover the feature's happy path
- [ ] New tests cover key error cases
- [ ] The feature works end-to-end in the running application
- [ ] No regressions in related features
- [ ] Code follows existing patterns and conventions
- [ ] Documentation updated if needed
- [ ] No leftover debug code, TODOs, or commented-out blocks

### If UI Is Involved

- Navigate the feature as a user
- Test on different viewport sizes if applicable
- Verify error states and edge cases
- Check that loading states work correctly

## Phase 5: Clean Up

1. Review the full diff before finalizing
2. Remove any temporary files or debug artifacts
3. Ensure all imports are used
4. Run linters if available
5. Final test run

## Common Failure Modes

| Failure | Mitigation |
|---------|------------|
| Breaking existing features | Run full test suite before and after |
| Not matching existing patterns | Read 2-3 similar features before implementing |
| Over-engineering the solution | Do the simplest thing that works correctly |
| Forgetting to update docs | Check instruction file for doc requirements |
| Leaving half-done work | Complete or revert — no half-states |
