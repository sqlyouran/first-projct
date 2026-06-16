## ADDED Requirements

### Requirement: `/pm` slash command

The system SHALL provide a `/pm <module> "<requirement>"` slash command at `.qoder/commands/pm.md` that triggers the PM agent to draft an OpenSpec change.

#### Scenario: PM command with valid module
- **WHEN** the user invokes `/pm patient-community "add AMA feature"`
- **THEN** the command SHALL invoke the PM agent with `module=patient-community` and the requirement text
- **AND** the PM agent's output SHALL be presented to the user for review before any files are written

#### Scenario: PM command without arguments
- **WHEN** the user invokes `/pm` with no arguments
- **THEN** the command SHALL list available modules from `docs/modules/*.md` and prompt for input
- **AND** SHALL NOT invoke the PM agent until a valid module is provided

### Requirement: `/qa:review` slash command

The system SHALL provide a `/qa:review <change>` slash command at `.qoder/commands/qa/review.md` that triggers the QA agent to perform product acceptance on a completed change.

#### Scenario: QA review of an applied change
- **WHEN** the user invokes `/qa:review <change-name>`
- **THEN** the command SHALL verify that the change exists at `openspec/changes/<change-name>/` and has `tasks.md` with all checkboxes marked
- **AND** SHALL invoke the QA agent with `change=<change-name>`
- **AND** the QA agent SHALL write a report to `docs/qa-reports/<YYYY-MM-DD>-<change-name>.md`

#### Scenario: QA review of an incomplete change
- **WHEN** the user invokes `/qa:review` on a change with unchecked tasks
- **THEN** the command SHALL refuse to proceed and report which tasks remain
- **AND** SHALL NOT invoke the QA agent

### Requirement: `/opsx:apply` smart dispatch

The existing `/opsx:apply` command SHALL be enhanced with a dispatch decision step that selects between direct execution, single-Engineer dispatch, or parallel BE+FE dispatch based on task characteristics.

#### Scenario: Small change goes direct
- **WHEN** `/opsx:apply <name>` is invoked on a change with fewer than 5 tasks
- **THEN** the orchestrator SHALL execute tasks directly without dispatching to any Engineer agent

#### Scenario: Cross-stack change dispatches both Engineers in parallel
- **WHEN** `/opsx:apply <name>` is invoked on a change whose tasks touch both `backend/` and `frontend/` directories AND the change has 5 or more tasks
- **THEN** the orchestrator SHALL partition tasks by stack
- **AND** SHALL dispatch backend tasks to `engineer-backend` and frontend tasks to `engineer-frontend` in a single parallel batch
- **AND** SHALL aggregate completion status from both Engineers

#### Scenario: Smoke test failure downgrades dispatch
- **WHEN** the smoke test for parallel subagent dispatch has failed (recorded in this change's tasks.md)
- **THEN** `/opsx:apply` SHALL NOT attempt parallel dispatch
- **AND** SHALL fall back to single-Engineer or direct execution

#### Scenario: Platform-layer change skips dispatch
- **WHEN** the change is classified as Platform-layer (modifies only `.qoder/`, `openspec/`, `docs/`, root configs)
- **THEN** the orchestrator SHALL execute directly without dispatching to any Engineer
