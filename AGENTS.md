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

### Starting a New Feature
1. Create a change directory: `openspec/changes/{feature-name}/`
2. Write `proposal.md` using template from `openspec/templates/`
3. Get proposal approved (review with user)
4. Write `design.md` with technical approach
5. Create `tasks.md` with atomic, testable tasks
6. Implement using the `feature-dev` skill

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

### Backend
- **Framework**: Spring Boot 3.4
- **Language**: Java 21
- **Build Tool**: Gradle (Kotlin DSL)
- **Database**: H2 (dev), configurable for production
- **Location**: `src/backend/`
- **Run**: `cd src/backend && ./gradlew bootRun`
- **Test**: `cd src/backend && ./gradlew test`
- **Port**: 8080

### Frontend
- **Framework**: React 19 + TypeScript
- **Build Tool**: Vite 6
- **Location**: `src/frontend/`
- **Run**: `cd src/frontend && npm run dev`
- **Build**: `cd src/frontend && npm run build`
- **Lint**: `cd src/frontend && npm run lint`
- **Port**: 5173 (dev proxy → backend:8080)

## Directory Structure
```
first-projct/
├── AGENTS.md              ← You are here (Harness orchestration)
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
├── src/
│   ├── backend/           ← Spring Boot application
│   │   ├── build.gradle.kts
│   │   ├── src/main/java/com/firstprojct/
│   │   └── src/test/java/com/firstprojct/
│   └── frontend/          ← React + Vite application
│       ├── package.json
│       ├── vite.config.ts
│       └── src/
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
