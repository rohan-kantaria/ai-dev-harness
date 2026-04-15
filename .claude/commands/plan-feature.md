Create a detailed implementation plan for a feature or phase.

The feature/phase name is in $ARGUMENTS (e.g., `/plan-feature phase-1-data-models`).

**Step 1 — Load context**
Read these files before doing anything:
- `CLAUDE.md` — tech stack, test command, lint command, naming conventions
- `.claude/PRD.md` — feature requirements and acceptance criteria
- `.claude/memory/preferences/testing_style.md` — how tests should be written

**Step 2 — Parallel codebase analysis**
Dispatch THREE subagents simultaneously. Provide each subagent with the relevant file list (from listing the project root). Give each a specific focus:

- **Subagent A (Structure):** List all existing source files, identify the file organization pattern, describe naming conventions observed, note any existing utilities or helpers that could be reused. If the project has no source files yet, report the structure of config files present.
- **Subagent B (Dependencies):** Identify all dependencies already installed (check package.json / requirements.txt / pyproject.toml / go.mod if present). Note which existing functions, classes, or utilities could be called directly. If no dependency files exist, report that.
- **Subagent C (Risks):** List 3-5 potential risks or edge cases for this feature based on the PRD. Identify where input validation is needed. Note any security considerations (SQL injection, auth checks, input sanitization). Note dependencies between steps that could cause ordering problems.

**Step 3 — Determine phase number**
Count files in `.agents/plans/` that are NOT `.gitkeep`. The new plan is `phase-[count+1]-[feature-name].md`.

**Step 4 — Write the implementation plan**

Write `.agents/plans/phase-[N]-[feature-name].md` with this structure:

```markdown
# Phase [N]: [Feature Name] — Implementation Plan

> **To implement:** Run `/execute .agents/plans/[this-filename]` to implement this plan step-by-step.

**Goal:** [one sentence describing what this phase delivers]
**Depends on:** [previous phases or "none — this is the first phase"]
**Total steps:** [count]

---

## Step 1: [action name]

**What:** [brief description]
**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py`
- Test: `tests/test_file.py`

- [ ] Write the failing test:
```python
def test_[specific_behavior]():
    # arrange
    # act
    result = [function call]
    # assert
    assert result == [expected]
```

- [ ] Run test — confirm it fails:
```bash
[test command from CLAUDE.md] tests/path/test_file.py::test_name -v
```
Expected: FAIL — "[error message indicating function not defined or similar]"

- [ ] Implement:
```python
[actual implementation code]
```

- [ ] Run test — confirm it passes:
```bash
[test command from CLAUDE.md] tests/path/test_file.py::test_name -v
```
Expected: PASS

- [ ] Run lint:
```bash
[lint command from CLAUDE.md]
```
Expected: no errors

- [ ] Commit:
```bash
git add [files]
git commit -m "[type]: [description]"
```

[Repeat for each step]

---

## Acceptance Criteria Checklist
[Copy the acceptance criteria from PRD.md for this feature]
- [ ] [criterion 1]
- [ ] [criterion 2]
```

**Important rules when writing the plan:**
- Every step must contain ACTUAL CODE — no "implement the function here" placeholders
- Every test step must contain the actual test to write
- Every command step must show the exact command and expected output
- Each step should be completable in 2-5 minutes
- The plan must cover ALL acceptance criteria from the PRD for this feature
- If the project has no source files yet (blank repo), the first step is always to create the project structure (e.g., `src/` or `app/` directory and `__init__.py`)

**Step 5 — Confirm**

Output:
```
✅ Plan written to .agents/plans/phase-[N]-[feature-name].md

Steps: [N] steps
Covers acceptance criteria: [list them]

Run: /execute .agents/plans/phase-[N]-[feature-name].md
```
