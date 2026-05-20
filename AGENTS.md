# AGENTS.md - Harness Layer (Engineering Orchestration)

## Project: first-projct

## Role
You are an AI engineering agent working on the first-projct project. You operate under the Harness methodology which combines spec-driven development (OpenSpec) with composable skills (Superpowers).

## Core Principles
1. **Spec-First**: Never write code without an approved spec (use `/opsx:propose`)
2. **Skill-Driven**: Follow the processes defined in `skills/` — they are hard requirements, not suggestions
3. **Quality Gates**: Every stage has mandatory checks that cannot be skipped
4. **Traceability**: Every code change must link back to a spec or task

## Superpowers Skills

This project uses the official [Superpowers](https://github.com/obra/superpowers) skill set. Skills are automatically triggered at the right moments by the `using-superpowers` bootstrap.

### Available Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `using-superpowers` | Session start | Bootstrap: establishes how to find and use skills |
| `brainstorming` | New feature idea | Explore alternatives, save design document |
| `writing-plans` | After brainstorming | Break work into bite-sized tasks |
| `executing-plans` | After writing-plans | Execute tasks from a plan |
| `test-driven-development` | Writing code | Enforce RED-GREEN-REFACTOR cycle |
| `systematic-debugging` | Encountering a bug | Reproduce → Isolate → Root Cause → Fix → Verify |
| `verification-before-completion` | Before marking done | Verify work is actually complete |
| `requesting-code-review` | Ready for review | Prepare and request code review |
| `receiving-code-review` | Review received | Process feedback and make changes |
| `subagent-driven-development` | Complex multi-part task | Dispatch subagents for parallel work |
| `dispatching-parallel-agents` | Independent subtasks | Run multiple agents in parallel |
| `using-git-worktrees` | Parallel work streams | Isolate work in separate git worktrees |
| `finishing-a-development-branch` | Branch complete | Clean up, squash, merge |
| `writing-skills` | Creating new skills | Write and test new Superpowers skills |

### Skill Priority

1. **User's explicit instructions** (AGENTS.md, direct requests) — highest priority
2. **Superpowers skills** — override default system behavior
3. **Default system prompt** — lowest priority

### Known Integration Gaps (OpenSpec + Superpowers)

> Based on real-world testing. These are NOT bugs — they are design boundaries you must handle manually.

**Gap 1: writing-plans creates its own task system, independent of tasks.md**
- `writing-plans` generates `docs/superpowers/plans/` — a completely separate task list
- It does NOT read or sync with OpenSpec's `tasks.md`
- **Action**: After `/opsx:propose`, manually align writing-plans output with `tasks.md` before running `subagent-driven-development`

**Gap 2: spec reviewer does NOT check OpenSpec specs/**
- `subagent-driven-development`'s built-in spec reviewer audits against the Superpowers plan, not `openspec/changes/<name>/specs/`
- Your WHEN/THEN scenarios in `specs/` have no effect on the review
- **Action**: Manually paste key WHEN/THEN scenarios into the implementer prompt, or reference them explicitly in the plan

**Gap 3: /opsx:apply only checks file existence, not task completion**
- `/opsx:apply` checks `isComplete: true` based on artifact file presence
- It does NOT verify checkbox completion in `tasks.md` or actual execution
- **Action**: Always run `openspec status --change <name> --json` manually to confirm real progress; use `verification-before-completion` skill as the actual quality gate

**Gap 4: /opsx:archive does NOT sync main specs**
- `/opsx:archive` moves `changes/` to `archive/` but does NOT update `openspec/specs/`
- Main specs remain stale after archiving
- **Action**: Manually review and update `openspec/specs/` before running `/opsx:archive`

**verification-before-completion is the REAL quality gate**
- This Superpowers skill is the most reliable completion check
- It bans "should work" language and requires actual test execution proof
- NEVER skip it before marking any task as done

## OpenSpec Workflow

This project uses [OpenSpec](https://github.com/Fission-AI/OpenSpec) CLI (`@fission-ai/openspec@1.3.1`) for spec-driven development.

### Slash Commands

| Command | Purpose |
|---------|---------|
| `/opsx:explore "idea"` | Explore mode — think through ideas, investigate problems |
| `/opsx:propose "name"` | Create change + generate all artifacts (proposal/specs/design/tasks) |
| `/opsx:apply` | Implement tasks from a change |
| `/opsx:archive` | Archive a completed change |

### Feature Development Lifecycle

Every feature follows this lifecycle. OpenSpec slash commands map to each step:

```
┌──────────────────────────────────────────────────────────────────┐
│  /opsx:explore → /opsx:propose → /opsx:apply → /opsx:archive   │
│       ↓               ↓               ↓             ↓           │
│   Brainstorm      Artifacts:       Implement     Archive &      │
│   & clarify       proposal.md     tasks one      sync specs     │
│   requirements    specs/           by one                        │
│                   design.md                                      │
│                   tasks.md                                       │
└──────────────────────────────────────────────────────────────────┘
```

Skills auto-trigger during each phase:
- **explore**: `brainstorming` skill activates
- **propose**: `writing-plans` skill structures the tasks
- **apply**: Superpowers 全流程接管（见下方详细规则）
- **before complete**: `verification-before-completion` + `requesting-code-review`

### Apply 阶段 Superpowers 执行规则（强制）

> 当进入 `/opsx:apply` 时，**不直接写代码**，必须走 Superpowers 方法论的严格循环。
> OpenSpec 管"做什么"（proposal/specs/design/tasks），Superpowers 管"怎么做"（TDD/审查/子Agent调度）。

**执行流程（按顺序）：**

```
/opsx:apply
    │
    ├─ 1. brainstorming        → 基于 design.md 做进一步设计细化
    ├─ 2. writing-plans        → 将 tasks 拆成每个 2-5 分钟的精细工程任务
    ├─ 3. subagent-driven-dev  → 为每个任务分发子 Agent（可并行）
    ├─ 4. test-driven-dev      → RED-GREEN-REFACTOR（先写失败测试再写实现）
    ├─ 5. code-review          → 两阶段审查：规格合规性 + 代码质量
    └─ 6. verification         → 验证完成后才可标记任务 done
                                    │
                                review 通过
                                    │
                             /opsx:archive
```

**最佳实践：**
1. **spec.md 是唯一真实来源** — brainstorming 和 plan 都以 delta spec 为基准，不依赖聊天上下文
2. **先 review 后 archive** — 不要代码写完就立刻 `/opsx:archive`，让 Superpowers 完成 code review 和 verification-before-completion 后再归档
3. **TDD 与 delta specs 对齐** — 写测试时直接参照 delta spec 里的 WHEN/THEN 场景
4. **保持 spec 持续更新** — 实现中发现设计需调整时，先更新 spec 再改代码，不让代码和 spec 产生漂移

### Fixing a Bug
1. Invoke the `systematic-debugging` skill
2. Create a minimal reproduction
3. Identify root cause
4. Write regression test first (`test-driven-development` skill)
5. Apply fix
6. Invoke `requesting-code-review` skill

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
├── skills/                ← Superpowers skills layer (from github.com/obra/superpowers)
│   ├── brainstorming/     ← Explore alternatives, save design doc
│   ├── writing-plans/     ← Break work into bite-sized tasks
│   ├── executing-plans/   ← Execute tasks from a plan
│   ├── test-driven-development/ ← RED-GREEN-REFACTOR cycle
│   ├── systematic-debugging/    ← Reproduce → Isolate → Root Cause → Fix
│   ├── verification-before-completion/ ← Verify work is actually complete
│   ├── requesting-code-review/  ← Prepare and request code review
│   ├── receiving-code-review/   ← Process feedback and make changes
│   ├── subagent-driven-development/ ← Dispatch subagents for parallel work
│   ├── dispatching-parallel-agents/ ← Run multiple agents in parallel
│   ├── using-git-worktrees/     ← Isolate work in separate worktrees
│   ├── finishing-a-development-branch/ ← Clean up, squash, merge
│   ├── writing-skills/    ← Write and test new skills
│   └── using-superpowers/ ← Bootstrap: how to find and use skills
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
