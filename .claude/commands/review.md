Review a completed implementation phase before marking it done and advancing.

If $ARGUMENTS contains a path, use that plan file. Otherwise, find the most recently modified file in `.agents/plans/` that is NOT `.gitkeep`.

If no plan file is found: output "No plan found. Run /plan-feature [name] to create one." and stop.

---

**Step 1 — Run tests**
Read `CLAUDE.md` for the test command. Run it.
Record: pass count, fail count.

**Step 2 — Run lint**
Read `CLAUDE.md` for the lint command. If lint command is "none" or not set: skip this step.
Run lint. Record: clean or N errors.

**Step 3 — PRD alignment**
Read `.claude/PRD.md`. Find the acceptance criteria for the phase being reviewed.

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
