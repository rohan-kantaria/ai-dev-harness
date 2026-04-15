Review a completed implementation phase before marking it done and advancing.

If $ARGUMENTS contains a path, use that plan file. Otherwise, find the most recently modified file in `.agents/plans/` that is NOT `.gitkeep`.

If no plan file is found: output "No plan found. Run /plan-feature [name] to create one." and stop.

---

**Step 1 — Run tests**
Read `CLAUDE.md` for the test command. Run it.
Record: pass count, fail count.

**Step 2 — Run lint**
Using the same CLAUDE.md already read in Step 1, extract the lint command. If the lint command is "none", not set, or still contains placeholder text (e.g., contains `[` or `run /init-project`): skip this step and record "⏭️ Skipped (not configured)".
Run lint. Record: clean or N errors.

**Step 3 — PRD alignment**
Read `.claude/PRD.md`. To find the acceptance criteria for this phase: use the plan filename (e.g., `phase-2-auth.md`) to identify the phase number and feature name, then search the PRD for a section with a matching phase number or feature name. If no exact match is found, include all PRD acceptance criteria and flag which ones could not be matched to this specific phase.

For each acceptance criterion, check:
- Is there code that implements it?
- Is there a test that verifies it?

Mark each as:
- ✅ covered — code + test both present
- ⚠️ partial — code exists but no test, or vice versa
- ❌ missing — neither code nor test

**Step 4 — Coverage check**
For each feature implemented in this phase:
- Is there a test for the happy path?
- Is there at least one failure/edge case test?

Note any gaps.

**Step 5 — Quick code scan**
Review files changed in this phase. Flag if any of these are present:
- Hardcoded values that should be named constants
- Missing input validation at user-facing entry points (API routes, CLI args, form inputs)
- Functions longer than ~30 lines (likely doing too much)
- Obvious security issues (string concatenation in SQL queries, unsanitized user input in responses)

**Verdict threshold — apply these rules strictly:**

APPROVED if ALL of the following are true:
- All tests pass (Step 1)
- Lint is clean OR was skipped because not configured (Step 2)
- No ❌ missing acceptance criteria (Step 3)
- No Critical code issues from Step 5

BLOCKED if ANY of the following are true:
- Any tests failing
- Lint has errors (and lint is configured)
- Any ❌ missing acceptance criterion (code or test absent)
- Any ⚠️ partial criterion where the missing piece is a test (code exists but no test = BLOCKED)
- Any ⚠️ partial criterion where the missing piece is code (test exists but no implementation = BLOCKED)
- Any security issue found in Step 5 (SQL injection, unsanitized user input)
- Any function over 30 lines found in Step 5

Note: A ⚠️ partial where only a minor edge-case test is missing (happy path and at least one failure case exist) can be APPROVED with a note. All other ⚠️ cases are BLOCKED.

**Step 6 — Output structured report**

```
## Phase Review: [phase name]

**Tests:** ✅ [N] passing / ❌ [N] failing
**Lint:** ✅ Clean / ❌ [N] errors / ⏭️ Skipped (not configured)

**PRD Alignment:**
- [criterion 1]: ✅ covered
- [criterion 2]: ⚠️ partial — [what's missing]
- [criterion 3]: ❌ not implemented

**Coverage Gaps:**
- [feature name]: missing [happy path / failure case] test

**Code Issues:**
- [file:line]: [specific issue]

---
**Verdict:** ✅ APPROVED — safe to advance to next phase
         OR ❌ BLOCKED — fix these items before advancing:
            1. [specific item]
            2. [specific item]
```

If APPROVED, suggest next action:
"Run `/plan-feature phase-[N+1]-[name]` to plan the next phase."

If BLOCKED, the developer should fix the listed items and re-run `/review` to get a fresh verdict.
