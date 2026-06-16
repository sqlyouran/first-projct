## ADDED Requirements

### Requirement: Module brain-dump template

The system SHALL provide a unified template at `docs/modules/_template.md` defining 8 mandatory sections for every product module brain-dump.

#### Scenario: Template structure is complete
- **WHEN** a contributor creates a new module brain-dump
- **THEN** the template SHALL include exactly these 8 sections in this order:
  1. Users and Value (persona / pain points / alternatives / core value proposition)
  2. User Journey (mermaid diagram)
  3. Current Capabilities (capability → user-facing description table)
  4. Red Lines and Anti-Goals
  5. Priority Principles
  6. Known Gaps / Tech Debt / Wishlist
  7. Interfaces with Other Modules (data / page / event dependency graph)
  8. Decision Log

#### Scenario: Deliberately omitted sections are documented
- **WHEN** a contributor looks for Roadmap, KPI, or Competitor Analysis sections
- **THEN** the template SHALL include a "Deliberately Omitted" footer explaining why these three sections are excluded in the first version

### Requirement: Three initial module brain-dumps

The system SHALL provide initial brain-dumps for all three product modules: `docs/modules/medical-directory.md`, `docs/modules/patient-community.md`, `docs/modules/hospital-inquiry.md`.

#### Scenario: Each brain-dump follows the template
- **WHEN** a PM or QA agent reads `docs/modules/<m>.md`
- **THEN** the file SHALL contain all 8 sections from the template, populated with module-specific content
- **AND** the "Red Lines" section SHALL contain at least 3 explicit anti-goals

#### Scenario: Brain-dump references existing OpenSpec specs
- **WHEN** a module has corresponding capabilities under `openspec/specs/<capability>/`
- **THEN** the brain-dump's "Current Capabilities" section SHALL list each capability with a link to its spec

### Requirement: Brain-dump maintenance is change-driven

Module brain-dumps SHALL be updated as part of any OpenSpec change that adds, modifies, or removes capabilities in that module.

#### Scenario: Change adds a new capability to a module
- **WHEN** an OpenSpec change introduces a new capability under a product module
- **THEN** the change's `tasks.md` SHALL include a task to update the relevant `docs/modules/<m>.md` "Current Capabilities" and (if applicable) "Decision Log" sections
- **AND** `/opsx:archive` SHALL NOT proceed until that task is checked

### Requirement: PM and QA agents must read brain-dump

PM and QA subagents SHALL be configured (via their system prompt) to read the relevant `docs/modules/<m>.md` before producing any output.

#### Scenario: Agent reads brain-dump first
- **WHEN** a PM or QA agent is invoked with a module argument
- **THEN** the agent SHALL read the brain-dump file as its first action
- **AND** SHALL cite at least one specific section of the brain-dump in its output (proposal Background or QA report)
