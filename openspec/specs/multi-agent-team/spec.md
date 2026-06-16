## ADDED Requirements

### Requirement: PM Agent definition and contract

The system SHALL provide a single Product Manager subagent at `.qoder/agents/product-manager.md` that drafts OpenSpec change artifacts (proposal/specs/tasks) using module brain-dumps as long-term context.

#### Scenario: PM agent invocation with module argument
- **WHEN** the orchestrator invokes the PM agent with `module=<name>` and a one-line requirement
- **THEN** the PM agent SHALL read `docs/modules/<name>.md` before drafting
- **AND** SHALL produce a draft proposal that references the module's "red lines" and "priority principles" sections

#### Scenario: PM agent encounters missing module brain-dump
- **WHEN** the requested module has no corresponding `docs/modules/<name>.md` file
- **THEN** the PM agent SHALL refuse to draft and return an error indicating the missing brain-dump
- **AND** SHALL NOT fabricate module context

#### Scenario: PM agent operates in default tier B
- **WHEN** no tier is specified
- **THEN** the PM agent SHALL produce a complete draft (proposal + specs + tasks)
- **AND** SHALL explicitly flag any decision points where multiple reasonable options exist
- **AND** SHALL NOT auto-execute `openspec new change` — file creation is left to the orchestrator

### Requirement: Engineer Agents definition and contract

The system SHALL provide backend and frontend Engineer subagents at `.qoder/agents/engineer-backend.md` and `.qoder/agents/engineer-frontend.md` (or a single merged `engineer.md` if the smoke test fails) that implement tasks from an OpenSpec change.

#### Scenario: Engineer agent receives task IDs
- **WHEN** the orchestrator invokes an Engineer agent with `change=<name>` and `tasks=<id-list>`
- **THEN** the Engineer SHALL read `proposal.md`, `design.md`, and relevant `specs/` before writing code
- **AND** SHALL invoke the `test-driven-development` skill for any new code path
- **AND** SHALL invoke the `verification-before-completion` skill before marking tasks complete

#### Scenario: Engineer agent marks tasks complete
- **WHEN** an Engineer agent finishes a task with verification passing
- **THEN** the Engineer SHALL update `tasks.md` to mark the corresponding checkbox as `[x]`
- **AND** SHALL NOT execute `git push` (push is a human-gated operation)

#### Scenario: Engineer encounters spec-vs-reality conflict
- **WHEN** the Engineer detects that current code or runtime contradicts the spec being implemented
- **THEN** the Engineer SHALL pause and escalate to the orchestrator with a structured conflict report
- **AND** SHALL NOT silently modify the spec or add TODO comments to defer the conflict

### Requirement: QA Agent definition and contract

The system SHALL provide a Product QA subagent at `.qoder/agents/qa.md` that performs static walkthrough from the user perspective and produces a markdown acceptance report.

#### Scenario: QA agent reviews a completed change
- **WHEN** the orchestrator invokes QA with `change=<name>`
- **THEN** QA SHALL read `proposal.md`, the relevant `docs/modules/<m>.md`, and the code diff
- **AND** SHALL produce `docs/qa-reports/<YYYY-MM-DD>-<change>.md` containing acceptance checklist, user-perspective issues sorted by severity, and suggested next-step changes
- **AND** SHALL NOT modify any code or test files
- **AND** SHALL NOT run unit tests (that is the Engineer's verification responsibility)

#### Scenario: QA agent has no module brain-dump available
- **WHEN** the change spans a module that lacks a brain-dump
- **THEN** QA SHALL still produce a report but SHALL explicitly mark each finding's confidence as "low — no module context"

### Requirement: Subagent collaboration via file contracts only

The system SHALL enforce that subagents do not communicate directly. All inter-agent handoff SHALL occur via shared files: `openspec/changes/<name>/*`, `docs/modules/*.md`, `docs/qa-reports/*.md`.

#### Scenario: Engineer needs information from PM
- **WHEN** an Engineer needs PM-level intent that is not in `proposal.md`
- **THEN** the Engineer SHALL escalate to the orchestrator (who may re-invoke PM), not attempt to invoke PM directly

### Requirement: Orchestrator role boundaries

The main conversation SHALL act as the sole orchestrator and SHALL handle: user entry points, Platform-layer changes (no PM dispatch), cross-module arbitration, dispatch decisions, and final aggregation.

#### Scenario: Platform-layer change
- **WHEN** a request concerns infrastructure (CI, deployment, env config) rather than a product module
- **THEN** the orchestrator SHALL implement directly without dispatching to PM
- **AND** SHALL NOT require a `docs/modules/<m>.md` brain-dump

#### Scenario: Cross-module requirement
- **WHEN** a single requirement plausibly belongs to multiple product modules
- **THEN** the orchestrator SHALL apply the arbitration rules in order: (1) entry-point ownership, (2) data ownership, (3) explicit human arbitration
- **AND** SHALL document the chosen module in the proposal's Background section
