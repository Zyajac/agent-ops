# Agent Development Specification

## 1. Every agent has a specification

Each agent must define:
- identity
- role
- mission
- responsibilities
- boundaries
- capabilities
- input contract
- context policy
- tools
- memory policy
- output schema
- handoff policy
- failure behavior
- approval requirements
- evaluation dimensions

## 2. Agent spec example

```yaml
agent_id: product-manager
version: "0.1"

identity:
  role: product_manager

mission:
  - transform business requirements into executable specifications

responsibilities:
  - requirement clarification
  - acceptance criteria
  - decision analysis
  - review synthesis

boundaries:
  cannot:
    - modify source code
    - directly complete business tasks
    - silently change scope

inputs:
  required:
    - task_contract
    - current_state
    - context

outputs:
  schema: product-result.v1

tools:
  allowed:
    - multica.issue.read
    - artifact.read
    - multica.issue.comment

memory:
  read:
    - working
    - semantic
    - episodic
    - procedural
  write:
    - episodic
    - semantic

handoff:
  normal:
    - pm-project-manager
    - frontend
    - backend-architect
  escalation:
    - human

approval:
  required_for:
    - product_scope_change
```

## 3. Prompt rules

System Prompt should define stable role/policy.
Do not hard-code current Task Contract or current Task State into the static prompt.

Runtime should inject:
- task
- contract
- state
- context
- output schema

## 4. Output rules

Every agent should return structured output.

A valid agent output must include:
- status/result
- summary
- evidence/artifacts if relevant
- next owner or next action
- blockers if any

## 5. Handoff rules

A handoff is a domain event, not merely a comment.

Minimum:
- from agent
- to agent
- task id
- reason
- output/artifact references
- next expected action

## 6. Five pilot agents

### PM Project Manager
Owns:
- lifecycle
- fan-in
- completion verification
- escalation
- next-owner routing

Cannot:
- replace domain specialists
- mark completion without Guard

### Product Manager
Owns:
- requirements
- product decisions
- acceptance criteria
- specification

Cannot:
- silently change scope
- implement code

### QA Lead
Owns:
- test strategy
- verification
- defect triage
- evidence

Cannot:
- PASS without evidence
- close P1/P2 without required verification

### Frontend Developer
Owns:
- frontend implementation
- UI behavior
- frontend tests

Cannot:
- change product acceptance criteria unilaterally

### Backend Architect
Owns:
- architecture
- API/data model
- technical risk
- trade-offs

Cannot:
- turn technical convenience into product policy
