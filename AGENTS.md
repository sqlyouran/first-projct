# AGENTS.md - Harness Layer (Engineering Orchestration)

## Project: first-projct

## Role
You are an AI engineering agent working on the first-projct project. You operate under the Harness methodology which combines spec-driven development (OpenSpec) with composable skills (Superpowers).

## Core Principles
1. **Spec-First**: Never write code without an approved spec in `openspec/changes/`
2. **Skill-Driven**: Follow the processes defined in `skills/` — they are hard requirements, not suggestions
3. **Quality Gates**: Every stage has mandatory checks that cannot be skipped
4. **Traceability**: Every code change must link back to a spec or task

## Workflow

### Feature Development Lifecycle (Mandatory)

Every feature/requirement MUST follow this 7-step lifecycle. No step can be skipped.

```
┌─────────────────────────────────────────────────────────────────┐
│  1. EXPLORE → 2. PROPOSE → 3. DESIGN → 4. TASKS               │
│       ↓           ↓            ↓           ↓                    │
│  Clarify      proposal.md  design.md   tasks.md                │
│  requirements  (approve)    (approve)   (breakdown)            │
│                                                                 │
│  5. IMPLEMENT → 6. REVIEW → 7. ARCHIVE                        │
│       ↓             ↓           ↓                              │
│  feature-dev    code-review  changes/ → archive/               │
│  + TDD skill    skill                                          │
└─────────────────────────────────────────────────────────────────┘
```

#### Step 1: Explore (需求探索)
- Clarify requirements with the user
- Ask questions to define scope, constraints, and success criteria
- Identify target users and key scenarios

#### Step 2: Propose (提案)
- Create directory: `openspec/changes/{feature-name}/`
- Write `proposal.md` using template from `openspec/templates/proposal.md`
- Content: problem statement, proposed solution, scope (in/out), success criteria
- **Gate**: Must get user approval before proceeding

#### Step 3: Design (技术方案)
- Write `design.md` using template from `openspec/templates/design.md`
- Content: architecture, data model, API design, frontend pages, dependencies
- **Gate**: Must get user approval before proceeding

#### Step 4: Tasks (任务拆解)
- Write `tasks.md` using template from `openspec/templates/tasks.md`
- Break into atomic, testable tasks grouped by phase
- Each task should be independently completable and verifiable

#### Step 5: Implement (实施)
- Follow `skills/feature-dev/SKILL.md` process
- For each task:
  - Write failing test first (`skills/tdd/SKILL.md`)
  - Implement minimum code to pass
  - Refactor if needed
- Run tests after each task: `cd backend && ./gradlew test`
- Run build after frontend changes: `cd frontend && npm run build`

#### Step 6: Review (代码审查)
- Invoke `skills/code-review/SKILL.md` process
- Review dimensions: Correctness, Security, Performance, Maintainability, Test Coverage
- Severity levels: 🔴 Critical → 🟡 Warning → 🟢 Suggestion
- **Gate**: All 🔴 Critical and 🟡 Warning issues must be fixed
- Re-run tests after fixes to confirm no regression

#### Step 7: Archive (归档)
- Move spec from `openspec/changes/{feature-name}/` to `openspec/archive/{feature-name}/`
- Commit with message: `chore: archive {feature-name} spec (feature complete)`
- Feature lifecycle is now complete

### Starting a New Feature
1. Begin at Step 1 (Explore)
2. Walk through all 7 steps sequentially
3. Never jump ahead — each step's gate must be passed

### Fixing a Bug
1. Invoke the `debugging` skill
2. Create a minimal reproduction
3. Identify root cause
4. Write regression test first (TDD skill)
5. Apply fix
6. Invoke `code-review` skill

### Code Quality
- Always invoke `code-review` skill before marking work complete
- Follow `tdd` skill for all new code
- Run lint and type checks before every commit

## Tech Stack

### Backend (git submodule: `backend/`)
- **Repo**: first-projct-backend
- **Framework**: Spring Boot 3.4
- **Language**: Java 21
- **Build Tool**: Gradle (Kotlin DSL)
- **Database**: H2 (dev), configurable for production
- **Run**: `cd backend && ./gradlew bootRun`
- **Test**: `cd backend && ./gradlew test`
- **Port**: 8080

### Frontend (git submodule: `frontend/`)
- **Repo**: first-projct-frontend
- **Framework**: React 19 + TypeScript
- **Build Tool**: Vite 6
- **Run**: `cd frontend && npm run dev`
- **Build**: `cd frontend && npm run build`
- **Lint**: `cd frontend && npm run lint`
- **Port**: 5173 (dev proxy → backend:8080)

## Repository Structure
This project uses **git submodules** to manage frontend and backend as independent repositories.

### Submodule Commands
- Init after clone: `git submodule update --init --recursive`
- Pull latest: `git submodule update --remote --merge`

## Directory Structure
```
first-projct/ (main repo)
├── AGENTS.md              ← You are here (Harness orchestration)
├── .gitmodules            ← Submodule declarations
├── package.json           ← Root scripts (dev/build/test shortcuts)
├── openspec/              ← Spec-driven development layer
│   ├── config.yaml        ← OpenSpec configuration
│   ├── specs/             ← Global project specifications
│   ├── changes/           ← Active feature changes
│   ├── templates/         ← Proposal/design/task templates
│   └── archive/           ← Completed specs
├── skills/                ← Superpowers skills layer
│   ├── tdd/SKILL.md       ← Test-driven development
│   ├── code-review/SKILL.md ← Code review process
│   ├── debugging/SKILL.md ← Debugging methodology
│   └── feature-dev/SKILL.md ← Feature development flow
├── backend/               ← [submodule] first-projct-backend
│   ├── build.gradle.kts
│   ├── src/main/java/com/firstprojct/
│   └── src/test/java/com/firstprojct/
├── frontend/              ← [submodule] first-projct-frontend
│   ├── package.json
│   ├── vite.config.ts
│   └── src/
├── tests/                 ← Integration / E2E tests
└── docs/                  ← Documentation
```

## Enforcement Rules
- ❌ Do NOT write implementation code without a spec
- ❌ Do NOT skip tests
- ❌ Do NOT merge without code review
- ❌ Do NOT suppress errors
- ✅ DO follow Red-Green-Refactor for all new code
- ✅ DO keep changes small and atomic
- ✅ DO update specs when requirements change
- ✅ DO archive completed specs
