Execute an implementation plan step-by-step using Test-Driven Development.

The plan file path is in $ARGUMENTS (e.g., `/execute .agents/plans/phase-1-setup.md`).

If $ARGUMENTS is empty or not provided: output "Usage: /execute <path-to-plan-file>" then list all files in `.agents/plans/` (excluding .gitkeep) as options. Stop.

**Before starting:**
1. Read the plan file at the given path. If the file does not exist, output: "❌ Plan file not found: [path]. Check .agents/plans/ for available plans." and stop.
2. Read `CLAUDE.md` — extract: test command, lint command
3. Read `.claude/memory/preferences/testing_style.md`
4. Read `.claude/memory/preferences/coding_style.md`
5. Identify the FIRST step with an unchecked checkbox (`- [ ]`)

If all steps are already checked (`- [x]`): output "✅ All steps complete. Run /review to validate the phase."

---

**For each incomplete step, classify its type by the step label text, then follow the matching procedure:**

- If the step label contains "Write the failing test", "Write test", or "test" → **WRITE TESTS** procedure
- If the step label contains "Implement" → **IMPLEMENT** procedure  
- If the step label contains "Run lint", "lint", or "Lint" → **RUN LINT** procedure
- If the step label contains "Commit" → **COMMIT** procedure
- Otherwise → **OTHER** procedure

### WRITE TESTS step
1. Create or open the test file specified in the plan's Files section, then write the test code exactly as shown in the plan
2. Run: `[test command] [specific test file]::[test name] -v`
3. The test MUST fail at this point
   - If it passes unexpectedly: PAUSE. Check if the function/feature under test already exists in the codebase. If yes: output "⚠️ [function name] already exists — this step may be redundant. Please review and confirm whether to skip this step or update the plan." and wait for the developer's response. If no: output "⚠️ Test passed before implementing but the implementation doesn't exist — the test may not be testing the right thing. Please review the test before proceeding." and wait. Do NOT auto-advance.
   - If it fails with the expected error: good — continue
4. Check the checkbox in the plan file for this step: change `- [ ]` to `- [x]`

### IMPLEMENT step
1. Write the implementation code shown in the plan
2. Run the FULL test suite: `[test command from CLAUDE.md]`
3. If all tests pass: check the checkbox and continue
4. If tests fail:
   - Read each failure message carefully
   - Fix ONLY the implementation code — NEVER modify a test to make it pass
   - Re-run the test suite
   - Repeat up to 3 attempts total (including the first run)
   - If still failing after 3 attempts: HALT. Output:
     ```
     ❌ Tests still failing after 3 attempts.

     Failing test: [test name]
     Error: [exact error message]

     I did not advance to the next step. Please review the failure above and either:
     - Fix the implementation manually and re-run /execute
     - Update the plan if the approach needs to change
     ```
5. Check the checkbox in the plan file

### RUN LINT step
1. Run: `[lint command from CLAUDE.md]`
2. If lint command is "none" or not set: skip this step, check the checkbox, continue
3. If clean: check the checkbox and continue
4. If errors:
   - Fix errors that can be auto-fixed (formatting, import ordering, simple style issues)
   - Re-run lint
   - If errors remain after 2 attempts: HALT and report which errors could not be fixed automatically
5. Check the checkbox

### COMMIT step
1. Read `.claude/memory/preferences/commit_style.md`
2. Stage the files listed in the plan step: `git add [files]`
3. Commit with the message from the plan (or generate one following the project's commit style if the plan says "generate"):
   - conventional: `git commit -m "feat: [description]"`
   - simple: `git commit -m "add [description]"`
4. Verify: `git log --oneline -1`
5. Check the checkbox

### OTHER step (setup, directory creation, etc.)
1. Execute the action described in the step
2. Verify it completed successfully (check that files/directories exist, commands returned 0)
3. Check the checkbox

---

**After each completed step:**
- Save the updated plan file immediately with the checkbox marked (`- [x]`)
- Output: `✅ Step complete: [step description]`
- Advance to the next unchecked step
- Repeat the procedure for each step

**When all steps are complete:**
```
🎉 Phase complete! All [N] steps done.

Summary:
- Tests written: [count]
- Tests passing: [count]
- Commits made: [count]

Next: Run `/review .agents/plans/[filename]` to validate before advancing to the next phase.
```

**General rules:**
- Never skip a step, even if it seems trivial
- Never mark a step complete until it fully passes
- Never modify tests to make them pass — fix the implementation
- Update the plan file checkbox in real-time (after each step completes)
- If the plan file contains a step type not covered above, read the step description carefully and execute it, then check the checkbox
- Always check the checkbox by editing the plan file — do not just mentally track progress
