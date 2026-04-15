---
name: Coding Style
description: Naming conventions, file structure, and code patterns for this project
type: user
---

## Environment

- **Always use a virtual environment — never install dependencies globally**
  - Python: use `venv` (`python -m venv .venv`) or `pipenv`
  - Node.js: dependencies are local by default via `package.json` — never use `-g` flags
  - Ruby: use `bundler`
  - Go: go modules are local by default
- **Always add the virtual env directory to `.gitignore`** (`.venv/`, `node_modules/`, etc.)
- **Always pin dependency versions** in the lock file (`requirements.txt`, `Pipfile.lock`, `package-lock.json`)

## Security

- **Never commit secrets** — API keys, passwords, tokens, connection strings
- **Always use a `.env` file** for secrets and add it to `.gitignore` immediately when the project is created
- **Always provide a `.env.example`** with placeholder values so others know what keys are needed
- **Before every commit:** scan staged files for hardcoded secrets (look for patterns like `sk-`, `Bearer `, `password =`, `api_key =`)

## Naming
- **Variables/functions:** [filled by /create-prd — e.g., snake_case]
- **Classes:** [e.g., PascalCase]
- **Files:** [e.g., snake_case.py]
- **Constants:** [e.g., UPPER_SNAKE_CASE]

## File Structure
- One clear responsibility per file
- Group by feature/domain, not by type (e.g., `tasks/` contains models, routes, and tests for tasks — not `models/`, `routes/`, `tests/` as top-level folders)
- Keep files under ~200 lines — if a file grows beyond that, it's doing too much

## Error Handling
- Validate at system boundaries only (user input, external API responses)
- Don't add error handling for scenarios that can't happen
- Raise specific exceptions, not generic ones
- Return meaningful error messages to users (not raw exception messages)

## Code Patterns
- No magic numbers — use named constants
- No nested ternaries
- Functions do ONE thing — if you need "and" to describe it, split it
- No commented-out code in commits
- No TODO comments in commits — create a plan step instead

## What to Avoid
- Premature abstraction (don't extract a helper until you need it in 3+ places)
- Feature flags for features that can just be implemented
- Backwards compatibility shims for internal code
- Speculative "we might need this later" code
