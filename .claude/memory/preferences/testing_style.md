---
name: Testing Style
description: Test framework, TDD conventions, and fixture patterns for this project
type: user
---

## Framework
[Filled by /init-project — e.g., pytest with pytest-asyncio for async tests]

## Test Command
```bash
[e.g., pytest tests/ -v --tb=short]
```

## File Naming
- Test files: `tests/test_<module_name>.py`
- Test functions: `test_<what_it_does>_<condition>` (e.g., `test_create_task_with_missing_title_returns_422`)

## TDD Rules
1. Write the test FIRST — run it to confirm it fails before implementing
2. Write the MINIMUM implementation to make the test pass
3. Only refactor after tests are green
4. Never modify a test to make it pass — fix the implementation instead
5. Every new behavior needs a test; every bug fix needs a regression test

## What to Test
- Happy path: the expected successful case
- One failure case: what happens when input is wrong or missing
- Boundary: edge values (empty string, zero, None, max length)

## What NOT to Test
- Private implementation details (internals that could change)
- Framework behavior (e.g., don't test that FastAPI handles JSON parsing)
- Third-party library behavior

## Fixture Patterns
[Filled by /init-project — e.g., use pytest fixtures for database setup/teardown, not global state]
