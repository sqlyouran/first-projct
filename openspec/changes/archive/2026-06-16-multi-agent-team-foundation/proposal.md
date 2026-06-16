## Why

当前项目所有的"想需求 / 写代码 / 跑验收"都由主对话单线程完成，缺少角色分工和并行能力。这导致：(1) 没有稳定的"产品视角"对照表，PM 思考容易跳过；(2) 跨栈 change 不能并行实施，wall-clock 时间被串行拖长；(3) 没有"用户视角"的产品验收环节，spec PASS 不等于用户体验 PASS。本次引入"PM + Engineer(BE/FE) + QA"四角色 AI 协作团队，把现有 Superpowers skills 重新组合成可派遣的 subagent，并为 PM 提供模块脑图作为长期上下文。

## What Changes

- **新增 PM Agent** (`.qoder/agents/product-manager.md`)：通用产品经理，按模块脑图起草 OpenSpec change（proposal/specs/tasks 草稿），默认采用"资深咨询型"档位（B），完整稿 + 标注分歧点
- **新增 BE/FE Engineer Agent**：Engineer 双栈拆分，主对话按 task 量分流 — 简单 change 直干、复杂或跨栈 change 并行派遣
- **新增 QA Agent** (`.qoder/agents/qa.md`)：定位 Product QA（不碰 Code QA），用静态走查从用户视角找问题，显式触发，产出 markdown 验收报告
- **新增模块脑图体系**：`docs/modules/` 目录下统一模板 + 3 份骨架（medical-directory / patient-community / hospital-inquiry），作为 PM 长期上下文
- **新增 slash commands**：`/pm <module> <需求>` 触发 PM 起草、`/qa:review <change>` 触发 QA 验收
- **修改 `/opsx:apply` 智能派遣策略**：根据 task 数量与跨栈判断，决定主对话直干 / 派遣单 Engineer / 并行派遣 BE+FE
- **打火试验前置**：实施第一组 task 强制验证 Qoder subagent 并行能力，结果决定 Engineer 走 E3（双栈并行）还是 E2（单栈降级）
- **AGENTS.md 增补**：4 agent 定位、跨模块仲裁规则、PM 档位协议、智能派遣策略

## Capabilities

### New Capabilities

- `multi-agent-team`: 定义 PM/BE/FE/QA 四个 subagent 的角色契约、输入输出规范、与主对话的编排关系、跨 agent 文件接力协议（基于 OpenSpec 工件）
- `module-knowledge-base`: 定义 `docs/modules/` 模块脑图体系 — 统一模板（8 段）、3 份初始骨架、维护规则（变更驱动更新）、PM 在起草前必读
- `agent-orchestration-commands`: 定义 `/pm`、`/qa:review` 两个新 slash 命令的输入/输出规范，以及 `/opsx:apply` 智能派遣策略（任务量阈值 / 跨栈判断 / 降级路径）

### Modified Capabilities

<!-- 无 — 本次为纯新增基础设施，不修改现有业务 capability -->

## Impact

- **代码影响**：零（不动 backend/、frontend/ 业务代码）
- **配置影响**：新增 `.qoder/agents/` 4 份、`.qoder/commands/` 2 份；修改 `.qoder/commands/opsx/apply.md`；修改根 `AGENTS.md`
- **文档影响**：新增 `docs/modules/` 4 份（1 模板 + 3 骨架）；新增 `docs/qa-reports/.gitkeep`
- **依赖**：不引入新依赖；强依赖 Qoder subagent 派遣能力（需打火试验）
- **风险**：subagent 并行能力若不可用，Engineer 实例化方案降级 E2（单 Engineer），整体设计不阻塞
- **可逆性**：所有新增工件均为 markdown 配置，可随时删除回滚
