# Playbook: Refactoring and Cleanup

> Use this playbook when the task is refactoring, tech debt reduction, code cleanup, or architectural improvement.

## When To Use

- Codebase has accumulated bad patterns that agents are replicating
- Module boundaries are unclear or violated
- Duplicated logic across files
- Outdated dependencies or deprecated APIs
- Documentation is stale or misleading
- Tests are flaky or missing

## Phase 1: Assess The Debt

### Steps

1. **Read the instruction file** and any existing debt/quality tracking
2. **Scan the codebase** for patterns:
   - Duplicated code blocks
   - Inconsistent naming or style
   - Dead code and unused imports
   - TODO/FIXME/HACK comments
   - Files over 500 lines
   - Circular dependencies
3. **Run linters and static analysis** if available
4. **Identify the "golden patterns"** — what does good code look like in this repo?
5. **Classify debt** by severity and blast radius

### Debt Classification

| Severity | Description | Priority |
|----------|-------------|----------|
| Critical | Actively causing bugs or blocking features | Fix immediately |
| High | Will cause issues if new features touch this area | Fix before related work |
| Medium | Makes the codebase harder to work with | Schedule for cleanup |
| Low | Cosmetic or stylistic inconsistencies | Fix opportunistically |

## Phase 2: Plan The Refactor

### Principles

- **Never refactor and add features in the same commit**
- **Preserve behavior** — refactoring should not change what the code does
- **Work in small, verifiable steps** — each commit should pass all tests
- **Refactor toward the existing good patterns**, not toward an ideal you invented

### Plan Structure

```markdown
## Refactor: [area or pattern]

### Goal
[What the codebase should look like after]

### Steps
1. [ ] [Specific change with affected files]
2. [ ] [Specific change with affected files]
3. [ ] [Specific change with affected files]

### Verification
- All existing tests pass after each step
- No behavior changes (unless explicitly fixing a bug)

### Risks
- [What could break]
- [Areas that depend on this code]
```

## Phase 3: Execute

### Per-Step Loop

1. Make one refactoring change
2. Run the full test suite
3. If tests fail: fix or revert. Never leave broken tests.
4. Commit with a descriptive message: `refactor: [what changed and why]`
5. Move to the next step

### Safe Refactoring Patterns

| Pattern | Approach |
|---------|----------|
| Extract function/module | Copy, then replace call sites one at a time |
| Rename symbol | Use IDE/tool rename, verify all references updated |
| Move file | Update all imports, verify build passes |
| Replace dependency | Add new alongside old, migrate callers, remove old |
| Simplify logic | Write characterization test first, then simplify |

### Dangerous Patterns To Avoid

- Rewriting a module from scratch instead of incrementally improving it
- Renaming things across the entire codebase in one commit
- Changing interfaces that external consumers depend on without versioning
- Removing "dead code" without verifying it's actually dead
- Reformatting files that have nothing to do with the refactor

## Phase 4: Verify

### Verification Checklist

- [ ] All tests pass (same results as before refactoring started)
- [ ] No behavior changes unless explicitly intended
- [ ] The application runs correctly end-to-end
- [ ] The refactored code follows the repo's golden patterns
- [ ] Dead code is actually dead (search for references before removing)
- [ ] Documentation updated if interfaces changed
- [ ] No new warnings or lint errors introduced

## Phase 5: Update The Harness

After significant refactoring, update the repo's harness:

1. Update the instruction file if module boundaries changed
2. Update architecture docs if structure changed
3. Remove stale documentation that referenced old patterns
4. Add new lint rules to enforce the improved patterns (so agents don't revert to old ones)
5. Update the debt tracker — remove resolved items, add any newly discovered items

## Entropy Management

Refactoring is garbage collection for codebases. To prevent debt from re-accumulating:

- **Encode new patterns as lint rules** — not just documentation
- **Add structural tests** that verify module boundaries
- **Create CI checks** that catch boundary violations
- **Update the instruction file** with the new constraints
- **Make the good pattern the easiest path** — agents follow the path of least resistance
