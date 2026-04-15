---
name: Project Decisions
description: Key technical decisions made during development and the rationale behind them
type: project
---

## Decision Log

<!-- Agents: add entries here during /execute when significant decisions are made -->
<!-- Format: ## [Date] Decision Title -->
<!-- Then: **Decision:** what was decided -->
<!-- Then: **Why:** the reason -->
<!-- Then: **Trade-off:** what was given up -->

## Example Entry

### 2026-01-01 — Use SQLite over PostgreSQL for MVP
**Decision:** SQLite as the database for the first version.
**Why:** No infrastructure setup, zero configuration, sufficient for local demo.
**Trade-off:** Not suitable for production multi-user workloads. Migrate to PostgreSQL when needed.
