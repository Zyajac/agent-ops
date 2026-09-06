# Architecture

## 1. Boundary

### Multica
Owns:
- Issue
- Project
- Agent
- Squad
- human-agent collaboration
- work surface
- comments/mentions
- existing runtime entry points

### Control Plane
Owns:
- Task Contract
- Business State
- Completion Guard
- Fan-out/Fan-in semantics
- normalized Events
- Context policy
- Memory policy
- governance
- evaluation metadata

### Agent Runtime
Owns:
- model execution
- agent loop
- tools/runtime
- streaming/session mechanics
- runtime checkpointing when available

## 2. Runtime-neutral interface

The Control Plane should never depend directly on a concrete LLM SDK.

Define:
- `AgentRunner`
- `ContextProvider`
- `ToolPolicy`
- `RunResult`

Then provide an adapter:
- `AgnoAgentRunner` initially, if Agno is selected.

## 3. Data flow

Task creation:
Multica -> adapter -> Task Service -> Contract validation -> persisted Task

Dispatch:
Task -> Context Builder -> Agent Runner -> Run

Delivery:
Agent -> RunResult -> Artifact/Evidence -> TASK_DELIVERED Event

Review:
TASK_DELIVERED -> Completion Guard -> Review / Rework / Approval

Completion:
Guard PASS -> fan-in/next owner -> state transition -> Multica projection

## 4. State ownership

`agent_tasks.state` is authoritative for business state.

Do not infer business completion only from:
- last comment
- agent text
- runtime success
- process exit code

## 5. Recommended deployment

v0.1:

Multica
  |
  | webhook/API/adapter
  v
Control API
  |
  +-- PostgreSQL
  |
  +-- Worker
  |
  +-- Agent Runtime Adapter
          |
          +-- Agno / other runtime

Redis is optional and should only be added if there is an actual queue/concurrency need.

## 6. Design principles

- contract over prose
- explicit state over inference
- evidence over claims
- events over polling
- fan-in over orphaned fan-out
- relevant context over full history
- adapters over vendor lock-in
- small control plane over a new framework
