# Evaluator Rubric Template

> Use this rubric to evaluate agent-generated work.
> Customize the criteria and weights for your specific project.
> The evaluator should inspect the running product, not just read code.

---

# Evaluation Rubric

## Scoring Scale

| Score | Meaning |
|-------|---------|
| 10 | Exceptional — exceeds expectations, production-ready |
| 8-9 | Strong — works well, minor issues only |
| 6-7 | Acceptable — functional but with notable gaps |
| 4-5 | Below threshold — significant issues need fixing |
| 1-3 | Failing — core functionality broken or missing |

## Criteria

### 1. Feature Completeness (Weight: High, Threshold: 7)

Does the implementation cover all specified features?

- Are all user stories from the spec implemented?
- Do features work end-to-end, not just UI shells?
- Are there any stubbed or half-implemented features?
- Does clicking every button actually do something?

**Common failures**: Features that appear in the UI but have no backend wiring. Buttons that exist but do nothing. Forms that render but don't submit.

### 2. Functionality (Weight: High, Threshold: 8)

Can users complete core workflows without errors?

- Do primary user flows work without bugs?
- Does error handling work (bad input, network errors, edge cases)?
- Are there any crashes or unhandled exceptions?
- Does the application state remain consistent?

**Common failures**: Race conditions on rapid user input. Broken navigation flows. Error states that crash the app instead of showing a message.

### 3. Design Quality (Weight: Medium, Threshold: 6)

Does the interface feel like a coherent whole?

- Do colors, typography, layout, and spacing form a consistent identity?
- Does the design have a clear visual hierarchy?
- Is there a deliberate aesthetic direction, or is it generic defaults?
- Does the layout use space effectively?

**Common failures**: Fixed-height panels wasting viewport space. Inconsistent spacing between sections. No clear visual hierarchy.

### 4. Originality (Weight: Low, Threshold: 5)

Are there evidence of deliberate creative choices?

- Is this distinguishable from a generic template?
- Are there custom design decisions, or all library defaults?
- Penalize telltale AI-generated patterns (purple gradients over white cards, etc.)

**Common failures**: Unmodified component library defaults. Generic "hero + cards + footer" layouts. Stock placeholder content.

### 5. Code Quality (Weight: Medium, Threshold: 7)

Is the code clean, maintainable, and well-structured?

- Clear separation of concerns
- No dead code or unused imports
- Proper error handling throughout
- Consistent naming conventions
- No hardcoded values that should be configurable
- Tests exist for critical paths

**Common failures**: Massive monolithic components. Duplicated logic across files. Error handling that swallows exceptions silently.

### 6. Craft (Weight: Low, Threshold: 6)

Technical polish and attention to detail.

- Typography hierarchy is clear and consistent
- Spacing follows a consistent scale
- Colors have proper contrast ratios
- Responsive behavior works at standard breakpoints
- Loading states and transitions are smooth

**Common failures**: Text that's too small to read. Buttons too close together on mobile. Jarring transitions between states.

## Evaluation Process

1. **Start the application** and verify it runs without errors
2. **Walk through every major user flow** — click every link, fill every form, test every feature
3. **Test edge cases** — empty states, long inputs, rapid clicks, browser back/forward
4. **Score each criterion** using the scale above
5. **Write specific findings** for any score below 8
6. **Determine pass/fail** — if any criterion is below its threshold, the sprint fails

## Output Format

```markdown
## Evaluation Results

### Scores
| Criterion | Score | Threshold | Pass/Fail |
|-----------|-------|-----------|-----------|
| Feature Completeness | X/10 | 7 | ✅/❌ |
| Functionality | X/10 | 8 | ✅/❌ |
| Design Quality | X/10 | 6 | ✅/❌ |
| Originality | X/10 | 5 | ✅/❌ |
| Code Quality | X/10 | 7 | ✅/❌ |
| Craft | X/10 | 6 | ✅/❌ |

### Overall: PASS / FAIL

### Findings
[Specific issues found, with reproduction steps]

### Recommendations
[What to fix in the next iteration, prioritized by severity]
```

## Evaluator Behavior Guidelines

- **Be skeptical by default** — assume things are broken until proven otherwise
- **Test, don't assume** — click the button, don't just read the click handler
- **Report concrete issues** — "Button X doesn't work when Y" not "the UX could be improved"
- **Resist the urge to praise** — focus on what's wrong, not what's right
- **Grade against the criteria** — not against your expectations of an AI
- **Do not talk yourself out of failures** — if something is broken, score it broken. Don't rationalize "but the rest is good"

## Calibration Guide

Out of the box, AI evaluators tend to identify issues then talk themselves into deciding they are not important. Calibration is essential.

### Step 1: Seed with few-shot examples

Provide 2-3 calibration examples in the evaluator prompt. Each example should include:

```markdown
### Calibration Example: Feature Completeness — Score 4 (FAIL)

**What was tested**: Dashboard with 5 specified widgets
**What was found**: 3 widgets rendered correctly, 1 showed static placeholder data, 1 was completely missing
**Why this scores 4**: 60% implementation with a missing feature = below threshold.
Do not rate this higher because "most features work" — missing features are not partial credit.
```

### Step 2: Run the calibration loop

1. Have the evaluator score the product
2. Review the evaluator's output yourself
3. Identify where its judgment diverges from yours (common: scoring 7 where you'd score 4)
4. Update the evaluator prompt with corrections targeting the specific divergences
5. Repeat until scoring aligns with human judgment

### Step 3: Watch for score drift

Within a long session, evaluators may become more lenient. If evaluation spans multiple sprints, reset the evaluator's context to prevent drift. Each evaluation should read the rubric fresh.

### Communication protocol

The evaluator writes results to `docs/evaluation-[sprint].md`. The generator reads this file and writes its response plan to `docs/response-[sprint].md`. This keeps all evaluation artifacts versioned and inspectable.
