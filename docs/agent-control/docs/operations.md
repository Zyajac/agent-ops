# Operations

## 1. Observability

Every run/event/task should be correlatable using:
- task_id
- run_id
- event_id
- agent_id
- correlation_id
- parent_run_id where applicable

## 2. Metrics

Track:
- task completion rate
- fan-out -> fan-in success rate
- average closure latency
- broken handoff count
- re-run rate
- timeout count
- human escalation rate
- input/output token usage
- context token usage
- memory recall count
- cost per task

## 3. Watchdog

Watchdog should be a fallback, not the primary execution mechanism.

Detect:
- RUNNING > timeout
- REVIEW > timeout
- BLOCKED > threshold
- no owner
- fan-out without fan-in
- orphan tasks
- repeated failures

## 4. Cost

Task governance may specify:
- token budget
- max runs
- max tool calls
- max parallelism

Threshold:
- warning at 80%
- block/escalate at 100%

## 5. Human approval

Require approval for:
- significant scope changes
- production-impacting changes
- P1/P2 closure where policy requires
- high-risk actions
- budget overruns
- unresolved conflicting decisions
