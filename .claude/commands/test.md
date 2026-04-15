Run the project test suite and fix any failures.

If $ARGUMENTS is empty: run the full test suite. If $ARGUMENTS contains a path (e.g., `/test tests/test_tasks.py`), run only that file.

**Step 1 — Get test command**
Read `CLAUDE.md` to get the test command (look for the "Test command:" line).

**Step 2 — Run tests**
Run the test command (or scoped to $ARGUMENTS if provided).

**Step 3 — Parse results**
- If all tests pass: output "✅ All tests passing. [N] tests in [X]s." and stop.
- If tests fail: continue to Step 4.

**Step 4 — Fix failures (up to 3 attempts)**
For each failing test:
- Read the test code to understand what behavior is expected
- Read the implementation file being tested
- Determine if the failure is a bug in the implementation or a genuine bug in the test itself
- If implementation bug: fix ONLY the implementation code
- If the test itself has a genuine bug (not just failing — actually wrong): report it and ask the developer before changing

Re-run the full test suite after each fix attempt. Repeat up to 3 total attempts.

**Step 5 — Report if still failing**
If tests still failing after 3 attempts:
```
❌ Tests still failing after 3 attempts.

Failing tests:
- [test name]: [error message]
[etc.]

I did not commit any changes. Please review the failures above.
```

**Rules:**
- Do NOT commit — /test is for checking and diagnosis only
- Never change a test to make it pass unless the test itself has a genuine bug
- Count "attempts" as full test suite runs, not individual fixes
