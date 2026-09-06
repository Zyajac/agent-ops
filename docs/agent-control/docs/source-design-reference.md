# Multica Agent Governance & Context Layer v0.1 — Source Design Reference

> Date: 2026-09-06
> Purpose: preserve the architectural rationale behind the Codex execution package.
> Canonical implementation rules are `../AGENTS.md`, the schemas, and `../tasks/T001-T020.md`.

## 1. Problem statement

The current Multica-based multi-agent team has several recurring engineering problems:

1. Agents can finish child work without a reliable fan-in mechanism.
2. Agents repeatedly reread long Issue histories, increasing context cost and latency.
3. Periodic polling can recover stalled work but creates latency and idle cost.
4. Comments are overloaded as human communication, state storage, memory and trigger mechanism.
5. System prompts can influence behavior but cannot replace machine-enforced task state and completion criteria.
6. Delivery is often treated as completion even when verification or approval is missing.
7. Parallel work, handoff, retry and approval are not represented through one consistent domain model.

The proposed solution is an **Agent Governance & Context Layer** above Multica, not another general-purpose multi-agent framework.

## 2. Layering

### Multica — Work OS

Multica remains responsible for Issues, projects, squads, assignments, comments and the human-agent collaboration surface.

### Governance & Context Layer — Business Control Plane

Owns:
- Task Contract
- Business State Machine
- Completion Guard
- Fan-out / Fan-in semantics
- normalized Events
- Context Builder
- Memory Policy
- governance limits
- evaluation metadata

### Agent Runtime — Execution Layer

A mature runtime such as Agno or another compatible runtime owns model calls, agent execution, tools, sessions and runtime-level concerns.

The custom Control Plane must remain runtime-neutral.

## 3. State distinction

Runtime state and business state are different.

Runtime examples:
- RUNNING
- WAITING
- FAILED
- RETRY
- CHECKPOINT

Business examples:
- CREATED
- PLANNED
- DISPATCHED
- IN_PROGRESS
- REVIEW
- APPROVAL_REQUIRED
- BLOCKED
- REJECTED
- COMPLETED

The Control Plane is authoritative for business state. Multica status is a projection/integration target.

An agent must not directly set a business task to COMPLETED.

## 4. Completion Guard

Business completion is a backend decision based on structured conditions, including:
- required criteria
- required evidence
- unresolved blocking defects
- required approvals
- fan-in completion
- handoff/next-owner requirements

This encodes the key principle: **delivery is not completion**.

## 5. Fan-out / Fan-in

Every fan-out operation must bind a fan-in owner and aggregation rule.

A parent task should carry:
- parent task id
- child task ids
- workers
- required/optional workers
- aggregation rule
- fan-in owner
- timeout/failure policy

MINT-179 is the reference scenario: multiple parallel reviews must automatically converge to synthesis and approval rather than relying on someone remembering to @mention the next agent.

## 6. Event-driven execution

The main path should be event-driven.

Normalized events replace prompt/comment conventions as the domain protocol. Examples:
- TASK_CREATED
- TASK_DISPATCHED
- AGENT_RUN_STARTED
- TASK_DELIVERED
- REVIEW_REQUESTED
- REVIEW_PASSED
- REVIEW_FAILED
- AGENT_HANDOFF
- APPROVAL_REQUESTED
- APPROVAL_GRANTED
- TASK_BLOCKED
- TASK_COMPLETED
- AGENT_RUN_FAILED

Periodic polling remains only as a watchdog for timeouts, orphan tasks and integration failures.

All event processing must be idempotent.

## 7. Context engineering

Do not send full Issue history on every run.

A Context Builder should assemble a bounded execution package:
1. role/system policy
2. task objective
3. Task Contract
4. current business state
5. completion criteria
6. relevant memory
7. recent relevant events
8. evidence/artifact references
9. allowed actions/tools
10. output schema

Context should be selected by relevance and budget rather than historical completeness.

## 8. Memory model

Use four logical memory layers:

### Working memory
Current run/task scratch state.

### Episodic memory
Past runs, incidents, outcomes and experiences that can help similar future work.

### Semantic memory
Relatively stable facts, project knowledge and domain information.

### Procedural memory
Repeatable methods, policies, workflows and operating instructions.

Persistent memory requires provenance. Not every chat turn should become memory.

## 9. Pilot agents

Initial pilot roles:
- PM Project Manager
- Product Manager
- QA Lead
- Frontend Developer
- Backend Architect

Each agent must have explicit responsibilities, forbidden actions, input/output schema, tool policy, memory policy, handoff policy and evaluation criteria.

## 10. Evaluation

Evaluation should cover more than final answer quality:
- instruction adherence
- output schema validity
- evidence quality
- task relevance
- handoff correctness
- scope compliance
- tool efficiency
- latency
- token/cost usage

## 11. Production principles

- event-driven main path, polling watchdog
- strong idempotency
- structured logs and correlation IDs
- retry with limits
- human approval for high-risk decisions
- cost/token/tool budgets
- evidence-based completion
- adapters around Multica and Agent Runtime

## 12. MVP boundary

The first implementation should remain deliberately small:
- one FastAPI control service
- PostgreSQL
- one worker
- Multica adapter
- Agent Runtime adapter

Do not introduce Kafka, Temporal, a vector database or a new dashboard until demonstrated requirements justify them.

## 13. Reference flows

### MINT-179

9 parallel reviews -> fan-in -> synthesis -> <=3 decision items -> approval -> Completion Guard -> COMPLETED.

### MINT-176

QA FAIL -> P1-1 defect -> development fix -> QA regression -> PASS with evidence -> Completion Guard -> COMPLETED.

These flows are the acceptance anchors for the v0.1 architecture.

## 14. Development order

Implement in this order:

Task Contract -> State Machine -> Completion Guard -> Fan-out/Fan-in -> Events -> Multica Adapter -> Context -> Memory -> Runtime Adapter -> Agents -> Evaluation/Cost -> Watchdog/HITL -> E2E pilots.

This order protects the project from becoming prompt-driven orchestration with hidden state.
