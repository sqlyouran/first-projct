## Context

项目当前由"主对话"作为唯一执行体，依托 Superpowers 的 13 个 skill（TDD、verification-before-completion、systematic-debugging 等）保证质量。OpenSpec 提供了 spec-driven 工作流（explore / propose / apply / archive），但所有阶段都是单线程串行执行。

已经存在的 `.qoder/agents/project-retrospective.md` 证明 Qoder 支持自定义 subagent。项目分为 3 个产品模块（medical-directory / patient-community / hospital-inquiry）+ Platform 层。前置探索阶段已经在 `important_decision_experience` 中固化了以下决策：

- PM 实例化：通用 PM + 模块脑图（方案 P2），不做"每模块一个 PM"
- PM 默认档位：B 资深咨询型（完整稿 + 标注分歧）；偶尔 C 助手型；不用 A 自治型
- 跨模块仲裁：3 条规则（入口归属 / 数据所有权 / 显式仲裁）
- QA 定位：Product QA，不碰 Code QA；静态走查 + 显式触发 + 报告归你拍板
- Engineer 实例化：E3 双栈并行（前提是 Qoder 并行能力可用，否则降级 E2）
- 落地节奏：单 change 全做（用户选 A 激进路线）

## Goals / Non-Goals

**Goals:**
- 用一组可派遣的 subagent 把"角色分工"从"主对话脑内切换"提升到"独立 system prompt + 文件契约"
- 通过双栈 Engineer 并行实现跨栈 change 的 wall-clock 缩短
- 用 `docs/modules/` 给 PM 提供持久、结构化的模块上下文，避免每次"从零讲背景"
- 引入 Product QA 环节，闭合"代码 PASS ≠ 产品 PASS"的盲区
- 通过打火试验前置，把"并行可行性"的风险在 30 分钟内查明，不让它污染后续工作

**Non-Goals:**
- 不做 E2E 自动化测试（Playwright/Cypress）— 当前不值得
- 不让 QA 自治产出 fix change（QA 只报告，新 change 由 PM 起草）
- 不让 Engineer 自己 `git push`（推送是人审操作）
- 不重构现有 Superpowers skills — 它们继续承担"方法论"职责，agent 只是"执行实体"
- 不在本 change 引入新的 capability spec（业务能力）— 这是基础设施 change
- 不做"自动触发 QA"或"自动审查"等任何自动化质量门 — 全部显式触发

## Decisions

### D1：4 agent 角色，主对话作为唯一编排者

**选择**：PM / Engineer-BE / Engineer-FE / QA 共 4 个 subagent。主对话承担 orchestrator 角色，所有用户入口、跨模块仲裁、Platform 层需求都由主对话处理。

**替代方案**：
- 5 agent（拆出独立 Orchestrator agent）— 否决：主对话本身就是 orchestrator，再拆一层徒增复杂度
- 3 agent（单 Engineer 不分栈）— 否决：放弃并行红利
- 2 agent（仅 PM + Engineer，无 QA）— 否决：丢失产品验收视角

### D2：跨 agent 协作通过文件契约，不通过对话

**选择**：subagent 之间不直接通信。所有交接通过共享文件：`openspec/changes/<name>/proposal.md` `specs/` `tasks.md`、`docs/modules/<m>.md`、`docs/qa-reports/<x>.md`。

**理由**：Qoder subagent stateless，每次调用是新会话；文件作为接力棒既符合平台特性，又天然形成审计追踪。

### D3：PM 实例化采用 P2 方案（通用 PM + 模块脑图）

**选择**：单一 `.qoder/agents/product-manager.md`，通过参数指定 module。模块上下文从 `docs/modules/<module>.md` 加载。

**替代方案**：
- P1 每模块一个 PM 文件 — 否决：3 份 prompt 维护成本高，且重复内容多
- P3 不要 PM agent — 否决：失去角色分工价值

### D4：Engineer 双栈采用 E3 方案，但有降级路径

**选择**：默认创建 `engineer-backend.md` 和 `engineer-frontend.md` 两个 agent；`/opsx:apply` 根据 task 量与跨栈判断决定派遣方式。

**降级触发**：打火试验失败 → Task 1.x 完成后合并为单 `engineer.md`，后续 task 自动调整。

**理由**：双栈实现的真正价值不是"职责清晰"而是"wall-clock 并行"，必须验证 Qoder 支持并行才有意义。

### D5：QA 定位为 Product QA，三个执行决策固定

**选择**：
- 决策 1（怎么看用户）：A 静态走查（从代码 + spec + 模块脑图推演场景）
- 决策 2（何时触发）：Y 显式触发 `/qa:review <change>`
- 决策 3（输出处理）：β 报告 + 你拍板 + 新 change 由 PM 起草
- 报告位置：`docs/qa-reports/<YYYY-MM-DD>-<change>.md`

**否决方案**：
- B 跑 E2E：当前无 E2E 框架，引入成本高
- α QA 自治产出 fix change：超出当前信任边界

### D6：智能派遣阈值与策略

| 触发条件 | 派遣方式 |
|---------|---------|
| Platform 层 change | 主对话直干（不派遣 PM） |
| Task 数 < 5 | 主对话直干 |
| Task 数 ≥ 5 且单栈 | 派遣对应单 Engineer |
| Task 数 ≥ 5 且跨栈 | 并行派遣 BE + FE Engineer |
| 打火试验失败时 | 全部降级为主对话直干或单 Engineer 串行 |

阈值 5 是首版经验值，归档 retrospective 时复盘调整。

### D7：模块脑图模板 8 段，强制必读

**8 段**：用户与价值 / 用户旅程 / 当前能力清单 / 红线与反目标 / 优先级原则 / 已知缺口 / 与其他模块接口 / 决策日志。

**故意省略**：Roadmap（变化快、易过期）、KPI（数据未就绪）、竞品对比（早期不必要）。

**PM 强制流程**：起草 change 前必须读取 `docs/modules/<module>.md`，prompt 中通过显式指令强制；如果脑图缺失对应模块，PM 必须报错而不是凭空起草。

### D8：打火试验作为第一组 task，门控后续

**形式**：30 分钟成本上限的最小验证 — 创建临时 `echo-test.md` agent，主对话同时派遣两个实例，比对返回时间戳。

**门控规则**：
- 通过 → 继续 E3 全部 task
- 失败 → 移除 BE/FE 拆分，合并为单 Engineer，后续 task 自动调整命名与内容

写进 tasks.md 顶部的"门控声明"块，确保任何执行者都先读到。

## Risks / Trade-offs

- **[R1] Qoder subagent 并行能力未验证** → 打火试验前置（Task 1.x），失败即降级 E2，不阻塞整体落地
- **[R2] PM 起草质量取决于脑图密度** → 默认档位 B（完整稿 + 标注分歧），你保留 review 权；首次起草不达标时反向补脑图
- **[R3] 主对话 token 消耗增加（每次派遣判断要读多份 context）** → 简单 change（<5 task）直接干不派遣；阈值复盘可调
- **[R4] QA 静态走查容易"水化"（建议全是通用项）** → 模块脑图"用户画像"段必须具体；QA prompt 强制引用具体段落
- **[R5] 跨 agent 上下文丢失（PM 微妙意图 Engineer miss）** → proposal "Background" 段强制写"为什么这么做"；Engineer prompt 强制读 Background
- **[R6] 一段式落地工作量大（>30 task）** → tasks.md 按 group 分组实施，每组完成后可暂停；激进路线由你显式确认

## Migration Plan

无运行时迁移。所有改动仅涉及 `.qoder/` `docs/` `openspec/` `AGENTS.md`，纯文档配置。

**部署步骤**：
1. 实施完成后 → `git commit + push`
2. 在新会话中验证 PM/QA agent 能被 Skill 工具调用
3. 用一个真实小 change（e.g. 给 community 加个小特性）端到端走完 PM → Engineer → QA 闭环

**回滚**：
- 软回滚：禁用 `.qoder/agents/*.md` 不删除，恢复主对话直干模式
- 硬回滚：`git revert` 单个 commit 即可

## Open Questions

- **OQ1**：`/pm` 命令是否需要支持"指定档位"参数？首版固定 B 档位，后续视使用频率决定
- **OQ2**：QA 报告积累后是否需要"周度汇总"？首版不做，等积累到 5+ 份再评估
- **OQ3**：Engineer 完成后是否需要自动发起 code review？首版不自动，避免每个小改动 noise；由你显式 `/review` 触发
- **OQ4**：模块脑图"决策日志"段是否需要规定格式（e.g. ADR 风格）？首版自由叙述，归档 retrospective 复盘
