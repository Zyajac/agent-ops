# AGENTS.md

## 1. Project

Project name: Multica Agent Governance & Context Layer.

Purpose:
Build a lightweight Control Plane above Multica.
Do not replace Multica and do not build a new general-purpose Agent Runtime.

The Control Plane owns business task semantics and governance.
Multica remains the Work OS / collaboration surface.
A mature Agent Runtime (initially Agno, unless the repository explicitly specifies another runtime) owns model execution.

## 2. Architecture

Canonical flow:

Multica
  -> Task Contract
  -> Business State Machine
  -> Event Processing
  -> Context Builder
  -> Agent Runtime
  -> Agent
  -> Evidence / Artifact
  -> Completion Guard
  -> Fan-in / Handoff
  -> Multica

Core modules:
- Task Contract
- Business State
- Completion Guard
- Fan-out / Fan-in
- Event Adapter
- Context Builder
- Memory Policy
- Evaluation / Observability
- Governance / Cost Control

## 3. Non-goals

Do NOT implement:
- a new LLM runtime
- a new agent loop
- a new vector database
- a new MCP runtime
- a new A2A runtime
- a new general-purpose workflow engine
- a new tracing platform
- a new enterprise IAM/RBAC platform
- a new Web Control Plane UI in v0.1

Prefer adapters and integration boundaries over duplication.

## 4. Source of Truth

Control Plane Business State is authoritative.

Multica status is a projection / integration target.
Avoid independent uncontrolled state mutations in both systems.

Agent runtime state is not the same as business task state.

## 5. Task Completion

An Agent must never directly declare a business task COMPLETED.

Allowed Agent-level result:
- READY_FOR_REVIEW
- PASS
- FAIL
- BLOCKED
- REQUIRES_HUMAN

Business COMPLETED requires Completion Guard to pass.

Completion Guard must check:
- required criteria
- required evidence
- blocking defects
- required approvals
- handoff / next-owner requirements

## 6. Fan-out / Fan-in

Every fan-out must define:
- parent task
- child tasks
- required workers
- aggregation rule
- fan-in owner

A parent must not complete until the required fan-in condition is satisfied.

## 7. Events

Comments, mentions, webhooks, timers and status changes are adapters.
The domain uses normalized events.

Every event should support:
- event_id
- event_type
- task_id
- actor/source
- occurred_at
- correlation_id
- causation_id
- payload
- idempotency_key

Event handling must be idempotent.

## 8. Context

Do not pass full historical Issue comments to agents by default.

Context Builder should assemble:
1. role/system policy
2. task objective
3. contract
4. current state
5. completion criteria
6. relevant memory
7. recent events
8. evidence/artifacts
9. allowed actions
10. output schema

Context is budgeted. Relevance is more important than historical completeness.

## 9. Memory

Use four logical categories:
- working
- episodic
- semantic
- procedural

Do not store every conversation turn as memory.

All persistent memory must have provenance:
- source task
- source run where available
- created/updated timestamps
- confidence/importance where applicable

Working memory is run-scoped.
Episodic memory describes past events/experiences.
Semantic memory stores relatively stable facts.
Procedural memory stores repeatable rules, methods, or instructions.

## 10. Agent Permissions

Agents operate under explicit capability/tool policies.

Agents MUST NOT:
- bypass Completion Guard
- overwrite historical Events
- delete another agent's evidence without explicit policy
- alter another task's Contract without permission
- bypass human approval
- expand task scope silently
- write arbitrary long-term memory

## 11. Coding Rules

- Prefer small, testable modules.
- Use strong typing.
- Use Pydantic models for API/domain schemas.
- Use PostgreSQL for persistence in v0.1.
- Use SQLAlchemy + Alembic unless the repository already standardizes another ORM/migration tool.
- Use FastAPI for HTTP API unless an existing repository standard overrides it.
- Use pytest for tests.
- Use structured logging.
- Keep domain logic independent of Multica adapters and Agent Runtime adapters.
- No unrelated refactors.

## 12. Development Workflow

Before coding:
1. Inspect the repository structure.
2. Read this file.
3. Read the task file.
4. Read only the directly relevant docs/schemas.
5. Identify existing conventions.
6. Propose the minimal implementation.

During coding:
- Preserve existing behavior.
- Add tests with each domain change.
- Keep external-system integration behind adapters.

After coding:
1. Run targeted tests.
2. Run the broader relevant test suite.
3. Report changed files.
4. Report test commands/results.
5. Report remaining risks.
6. Do not claim completion if acceptance criteria are not met.

## 13. Task Order

Implement tasks in dependency order:

T001 repository/bootstrap
T002 schemas
T003 task domain + persistence
T004 business state machine
T005 completion guard
T006 fan-out/fan-in
T007 events
T008 Multica adapter
T009 context builder
T010 memory service
T011 runtime adapter
T012 PM agent
T013 Product agent
T014 QA agent
T015 Frontend agent
T016 Backend Architect agent
T017 evaluation/cost
T018 watchdog/HITL
T019 E2E pilot MINT-179
T020 E2E pilot MINT-176

Do not skip foundational tasks unless the repository already implements an equivalent capability.

## 14. Definition of Done

A task is done only when:
- implementation exists
- acceptance criteria pass
- automated tests pass
- integration boundaries are documented
- no known critical regression remains
- output is summarized for the next owner

Agent completion and business task completion are different concepts.
