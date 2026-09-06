# Codex Task Index

Execute in order unless an existing repository capability makes a task unnecessary.

| Task | Scope | Depends on |
|---|---|---|
| T001 | bootstrap | none |
| T002 | schemas | T001 |
| T003 | task domain | T002 |
| T004 | state machine | T003 |
| T005 | completion guard | T004 |
| T006 | fan-in/fan-out | T005 |
| T007 | events | T004 |
| T008 | Multica adapter | T007 |
| T009 | context | T003, T007 |
| T010 | memory | T009 |
| T011 | runtime adapter | T009 |
| T012 | PM agent | T011 |
| T013 | Product agent | T011 |
| T014 | QA agent | T011 |
| T015 | Frontend agent | T011 |
| T016 | Backend Architect | T011 |
| T017 | eval/cost | T011 |
| T018 | watchdog/HITL | T007, T017 |
| T019 | MINT-179 | T012-T018 |
| T020 | MINT-176 | T012-T018 |
