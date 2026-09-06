# AGENTS.md — Multica Agent Control

## Purpose

This repository contains the implementation of the Multica Agent Governance & Context Layer.

The authoritative development specification for this project lives under:

`docs/agent-control/`

Before implementing or modifying Agent Control functionality, Codex MUST read the relevant files in that directory.

## Required reading order

For every Agent Control task:

1. Read this file.
2. Read `docs/agent-control/AGENTS.md`.
3. Read `docs/agent-control/docs/architecture.md`.
4. Read the task file under `docs/agent-control/tasks/` that corresponds to the requested task.
5. Read any additional specification referenced by that task, including schemas and agent specifications.
6. Inspect the existing repository implementation before writing code.

Do not implement from the task title alone.

## Project boundary

Build a lightweight governance/control layer ABOVE Multica.

Multica remains the Work OS and collaboration surface.

The Agent Control layer owns business execution semantics such as:

- Task Contract
- Business State Machine
- Completion Guard
- Fan-out / Fan-in
- normalized Events
- Context Builder
- Memory Policy
- Agent governance
- evaluation and cost controls

Do NOT turn this repository into another general-purpose agent framework.

Unless a task explicitly changes this decision, do not build replacements for:

- LLM runtime
- generic agent loop
- vector database
- MCP runtime
- A2A runtime
- general workflow engine
- tracing platform
- enterprise IAM/RBAC
- separate Web Control Plane UI

Reuse existing/runtime capabilities where possible.

## Core invariants

These rules are mandatory unless an explicit architecture decision changes them:

1. Control Plane business state is authoritative.
2. Multica status is a projection/integration state, not the source of truth for business completion.
3. An Agent MUST NOT directly mark a business task `COMPLETED`.
4. Completion is determined by the Completion Guard from criteria, evidence, blockers, approvals and handoff requirements.
5. Every fan-out MUST define a fan-in owner and aggregation rule.
6. `@mention` is not the canonical orchestration protocol.
7. Event-driven execution is the primary path; polling is a watchdog/recovery mechanism.
8. Context Builder MUST NOT send full issue history by default.
9. Memory is separated into working, episodic, semantic and procedural categories.
10. Agent delivery is not equivalent to business task completion.
11. Side effects must be idempotent or protected by idempotency keys.
12. Domain logic must remain independent from Multica and Agent Runtime adapters.

## Canonical execution flow

`Multica -> Task Contract -> Business State Machine -> Event Processing -> Context Builder -> Agent Runtime -> Agent -> Evidence/Artifact -> Completion Guard -> Fan-in/Handoff -> Multica`

## Implementation workflow

When asked to implement a task such as `T005`:

1. Open `docs/agent-control/tasks/T005.md`.
2. Resolve and read its dependencies.
3. Inspect existing code and tests.
4. Make the smallest coherent implementation that satisfies the specification.
5. Add/update unit and integration tests.
6. Run relevant tests.
7. Do not weaken tests merely to make them pass.
8. Report changed files, tests executed, results, assumptions and remaining risks.

If the repository already contains a capability required by a task, extend or adapt it instead of creating a duplicate subsystem.

## Development order

The default implementation sequence is:

`T001 -> T002 -> T003 -> T004 -> T005 -> T006 -> T007 -> T008 -> T009 -> T010 -> T011 -> T012-T016 -> T017 -> T018 -> T019 -> T020`

Parallel work is allowed only when task dependencies permit it.

## Coding expectations

Preferred baseline unless the repository establishes another standard:

- Python
- FastAPI
- PostgreSQL
- Pydantic
- SQLAlchemy / Alembic
- pytest
- type hints
- structured logging

Keep domain rules testable without external services.

Keep adapters behind interfaces/ports.

Do not place business rules inside HTTP handlers, webhook handlers, prompts, or Multica-specific code.

## Agent development

Agent roles and boundaries are defined under:

`docs/agent-control/docs/agents/`

The first pilot roles are:

- PM Project Manager
- Product Manager
- QA Lead
- Frontend Developer
- Backend Architect

Agent prompts are implementations of these specifications, not the authoritative source of business rules.

Agents may return execution outcomes such as:

- `READY_FOR_REVIEW`
- `PASS`
- `FAIL`
- `BLOCKED`
- `REQUIRES_HUMAN`

They may not bypass the Completion Guard.

## Schemas

Machine-readable contracts live in:

`docs/agent-control/schemas/`

Treat schemas and domain models as public contracts. Changes must remain synchronized with tests, persistence and API behavior.

## Acceptance rule

A coding task is not done merely because code was generated.

A task is considered implementation-complete only when:

- requested behavior is implemented,
- relevant acceptance criteria are satisfied,
- tests pass,
- no known dependency/invariant is violated,
- and remaining risks or incomplete external integrations are explicitly reported.

Business task completion remains subject to the Agent Control Completion Guard.
