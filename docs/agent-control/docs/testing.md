# Testing Strategy

## 1. Unit tests

Required:
- contract validation
- state transitions
- transition guards
- completion guard
- fan-in guard
- event normalization
- event idempotency
- context selection
- memory policy

## 2. Integration tests

Required:
- PostgreSQL persistence
- Multica adapter
- Agent Runtime adapter
- event -> state transition
- evidence -> completion guard
- handoff -> next owner

## 3. End-to-end tests

### MINT-179

Expected:
9 worker reviews
-> fan-in
-> synthesis
-> <=3 decision items
-> PM/human approval
-> COMPLETED

Failure injection:
- one worker fails
- one worker times out
- missing evidence
- duplicate delivery event

### MINT-176

Expected:
QA FAIL
-> P1-1 defect
-> development fix
-> QA regression
-> PASS
-> completion guard
-> DONE

Failure injection:
- QA result exists but evidence missing
- approval missing
- duplicate event
- timeout

## 4. Agent evaluation

At minimum:
- instruction adherence
- output schema validity
- task relevance
- evidence quality
- handoff correctness
- scope compliance

## 5. Regression requirement

Every new domain change must run:
- targeted unit tests
- affected integration tests
- existing regression suite
