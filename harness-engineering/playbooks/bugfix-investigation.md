# Playbook: Bug Investigation and Fix

> Use this playbook when investigating and fixing bugs — from user reports, failing tests, or discovered during development.

## Phase 1: Reproduce

Before fixing anything, reproduce the bug reliably.

### Steps

1. **Read the bug report** — what is the expected behavior vs. actual behavior?
2. **Reproduce the bug** in the running application
3. **Find the minimal reproduction** — the smallest steps that trigger the issue
4. **Document the reproduction steps** clearly

### Reproduction Template

```markdown
## Bug: [short description]

### Expected
[What should happen]

### Actual
[What actually happens]

### Steps to Reproduce
1. [Step 1]
2. [Step 2]
3. [Step 3]

### Environment
- [Browser/OS/runtime version if relevant]
- [Any special configuration]
```

### If You Cannot Reproduce

- Check if the bug is environment-specific
- Check if it requires specific data or state
- Check if it's a race condition (try running multiple times)
- Ask for more details before proceeding

## Phase 2: Diagnose

### Investigation Strategy

1. **Start from the symptom** — where does the error appear?
2. **Trace backward** — what code path leads to this point?
3. **Identify the root cause** — not just where it breaks, but why

### Investigation Tools

| Tool | When to Use |
|------|-------------|
| Error messages and stack traces | First thing to check |
| Git blame / git log | When did this code last change? |
| Application logs | What happened before the error? |
| Debugger | For complex control flow or state issues |
| Browser DevTools | For frontend issues (network, console, DOM) |
| Test isolation | Write a failing test that captures the bug |

### Common Root Causes

- **Incorrect condition**: Logic check is wrong (off-by-one, wrong operator)
- **Missing guard**: No check for null, empty, or unexpected input
- **Race condition**: Timing-dependent behavior
- **State mutation**: Shared state modified unexpectedly
- **API mismatch**: Caller and callee disagree on contract
- **Route ordering**: Framework matches wrong route (e.g., `:id` matches `reorder`)

## Phase 3: Write A Failing Test

Before fixing the bug, write a test that fails because of the bug.

This:
- Proves you understand the root cause
- Prevents regression
- Gives you confidence the fix actually works

```
# Pattern:
1. Write test that exercises the buggy behavior
2. Run test → verify it fails
3. Fix the bug
4. Run test → verify it passes
5. Run full test suite → verify no regressions
```

## Phase 4: Fix

### Rules

- **Fix the root cause**, not the symptom
- **Make the smallest change that fixes the bug** — no drive-by refactoring
- **If the fix is complex, explain why** in the commit message
- **Check for the same pattern elsewhere** — if the bug exists in one place, it likely exists in similar code

### Fix Verification

1. Run the failing test → it should pass now
2. Run the full test suite → no regressions
3. Reproduce the original bug → it should be fixed
4. Test edge cases around the fix

## Phase 5: Commit and Document

### Commit Message

```
fix: [what was broken] — [what the fix does]

[Root cause explanation]
[How the fix addresses it]

Fixes #[issue number if applicable]
```

### Documentation

- If the bug revealed a gap in docs, update them
- If the bug was caused by a non-obvious design decision, document the decision
- If the bug was in a known fragile area, update the quality tracker

## Phase 6: Prevent Recurrence

After fixing, consider:

1. **Can a lint rule catch this pattern?** → Add it
2. **Is this a class of bugs, not just one instance?** → Search for similar patterns
3. **Was the test coverage adequate?** → Add missing tests for the module
4. **Was the bug discoverable from the instruction file?** → Update it if the area is fragile

## Common Failure Modes

| Failure | Mitigation |
|---------|------------|
| Fixing the symptom, not the cause | Always trace to root cause |
| Not writing a regression test | Mandatory: failing test before fix |
| Fix breaks something else | Run full test suite after every fix |
| Same bug class keeps appearing | Add a lint rule or structural test |
| Drive-by refactoring in the fix PR | Separate commits for fixes and refactors |
