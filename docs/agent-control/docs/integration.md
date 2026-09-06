# Integration Specification

## 1. Multica Adapter

The adapter translates between Multica and normalized Control Plane domain events.

Inbound examples:
- issue created
- assignee changed
- comment created
- agent mention
- webhook
- status changed
- artifact delivered

Outbound examples:
- update issue
- assign agent
- create comment
- mention next owner
- request approval

## 2. Important rule

`@agent` is an integration trigger, not the domain communication protocol.

Example:

Multica comment:
`@product-manager please review`

becomes:
`AGENT_HANDOFF`

The Control Plane records the event and routes it according to Task Contract.

## 3. Idempotency

Every inbound event must have a deterministic idempotency key.

Example:
`comment:{multica_comment_id}`

Do not execute the same event twice.

## 4. Runtime Adapter

Define an interface:

```python
class AgentRunner(Protocol):
    async def run(self, request: AgentRunRequest) -> AgentRunResult: ...
```

`AgentRunRequest` contains:
- task_id
- run_id
- agent_id
- context
- allowed_tools
- output_schema
- governance limits

`AgentRunResult` contains:
- status
- structured output
- artifact ids
- tool-call metadata
- token usage if available
- runtime metadata

## 5. Artifact boundary

Agent outputs should reference artifacts through stable IDs/URIs.
Do not place large artifacts into comments.

## 6. Failure behavior

Adapter failures should produce:
- AGENT_RUN_FAILED or INTEGRATION_FAILED
- diagnostic payload
- retry decision
- correlation metadata
