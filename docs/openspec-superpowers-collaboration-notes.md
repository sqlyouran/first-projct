# OpenSpec + Superpowers 协作实战笔记

> 来源：腾讯云开发者社区实战文章《OpenSpec + Superpowers 协作实战：规格驱动开发能走多远？》
> 整理日期：2026-05-19

## 核心结论

**自动串联不成立。分开用都是好工具，但别指望无缝流水线。**

两个工具在各自的设计意图内表现良好，但它们之间没有原生的双向数据同步机制，需要人工在关键节点进行对齐。

---

## 实测可行路径（4条）

| 路径 | 实测结果 |
|------|---------|
| `openspec init` + 安装 Superpowers | 环境搭建流畅 |
| `/opsx:propose` → 生成4个工件 | 规格产出质量不错 |
| `brainstorming`（手动 prompt 触发）| AI 能读取 OpenSpec 工件作为参考 |
| `subagent-driven-development` | 子代理流程正常运行 |

---

## 已确认断裂点（4条）

### 断裂点 1：writing-plans 独立生成任务系统，不读 tasks.md

**现象**：
- `brainstorming` 完成后触发 `writing-plans`
- writing-plans 在 `docs/superpowers/plans/` 生成自己的任务列表（7个Task，每个2-4步）
- 与 OpenSpec 的 `openspec/changes/<name>/tasks.md`（4阶段11子任务）完全独立，互不同步

**两套任务系统对比**：

| 系统 | 位置 | 粒度 | 生成方式 |
|------|------|------|---------|
| OpenSpec tasks.md | `openspec/changes/<name>/tasks.md` | 4阶段 11子任务 | `/opsx:propose` 生成，手动维护 |
| Superpowers plan | `docs/superpowers/plans/` | 7个Task，每个2-4步 | `writing-plans` 自动生成 |

**应对措施**：
- `/opsx:propose` 生成后，先跑 `writing-plans`
- 手动对比两套任务，人工对齐差异
- 以 OpenSpec `tasks.md` 为主，将 Superpowers plan 中遗漏的任务手动补充进去

---

### 断裂点 2：spec reviewer 不检查 OpenSpec specs/

**现象**：
- `subagent-driven-development` 内的 spec reviewer 审查基准是 Superpowers 自己的 plan 文件
- 完全不读 `openspec/changes/<name>/specs/` 目录
- 你写的 WHEN/THEN 场景对子代理的审查没有任何影响

**应对措施**：
- 在 implementer prompt 中手动引用关键 WHEN/THEN 场景
- 或在 `openspec/config.yaml` 的 `context` 字段显式描述验收标准
- 不要依赖 spec reviewer 来验证 OpenSpec specs 中的场景

---

### 断裂点 3：/opsx:apply 只检查工件存在，不验证任务完成

**现象**：
- `/opsx:apply` 执行后检查 `isComplete: true`，但这只代表工件文件存在
- 不检查 `tasks.md` 中的 checkbox 完成情况
- 不代表任务被实际执行

**应对措施**：
- 每次 apply 后手动运行：
  ```bash
  openspec status --change <name> --json
  ```
- 用 `verification-before-completion` skill 作为真正的完成质量门
- 不依赖 `/opsx:apply` 的返回状态判断完成

---

### 断裂点 4：/opsx:archive 不同步主规格

**现象**：
- `/opsx:archive` 只是把 `openspec/changes/<name>/` 移动到 `openspec/changes/archive/`
- 不会将 delta specs 同步到 `openspec/specs/`（主规格目录）
- archive 之后主规格仍然是旧版本
- archive 也不检查 `tasks.md` 是否全部完成

**应对措施**：
- archive 前手动检查：
  ```bash
  openspec status --change <name> --json  # 确认所有工件完成
  ```
- 手动将 `changes/<name>/specs/` 中的增量规格合并到 `openspec/specs/`
- 确认无误后再执行 `/opsx:archive`

---

## 未验证项（1条）

- `specs/` 中的 WHEN/THEN 场景 → TDD 测试映射：理论上可行，但子代理实测中从未自动触发 `test-driven-development` skill

---

## subagent-driven-development 使用细节

子代理执行流程：

```
读取计划 → 分派实现者 → 规格合规审查 → 代码质量审查 → 标记完成
               ↑                                    |
               └──────────── 不通过则修复 ────────────┘
```

子代理完成后状态说明：

| 状态 | 含义 | 处理方式 |
|------|------|---------|
| `DONE` | 完成，审查通过 | 进入下一个任务 |
| `DONE_WITH_CONCERNS` | 完成但有顾虑 | 检查偏差点，评估是否接受 |
| `NEEDS_CONTEXT` | 信息不足 | 补充 specs/、design.md 内容 |
| `BLOCKED` | 被阻塞 | 检查阻塞原因，提供缺失信息 |

**关键约束（不得跳过）**：
- 不在 main/master 分支直接实现，必须用独立分支
- 不跳过任何审查环节（即使耗时）
- 规格合规审查通过前不启动代码质量审查
- 子代理不继承主会话上下文，控制器必须在 prompt 中提供完整信息

---

## 常见故障排查

### 故障 1：子代理审查死循环

**症状**：spec reviewer 和 code quality reviewer 反复打架，循环 3+ 轮不结束

**根因**：reviewer 的审查标准不清晰，或 plan 中缺少可量化的验收条件

**解决方案**：
1. 暂停，检查 spec reviewer 的审查基准是什么
2. 在 plan 中补充明确的 WHEN/THEN 场景（即使 spec reviewer 不读 OpenSpec specs，也要在 plan 里写）
3. 审查超过 3 轮强制推进，审查意见记录为 `DONE_WITH_CONCERNS`

---

### 故障 2：两套任务系统脱节导致实现偏差

**症状**：Superpowers 子代理按照自己的 plan 实现，与 OpenSpec tasks.md 描述的功能不一致

**根因**：writing-plans 生成时没有参考 OpenSpec tasks.md

**解决方案**：
1. writing-plans 执行前先阅读 OpenSpec tasks.md
2. 手动对比两套任务，让 AI 将差异补充进 Superpowers plan
3. apply 执行前以 OpenSpec tasks.md 为准做最终确认

---

### 故障 3：子代理 NEEDS_CONTEXT 或 BLOCKED

**症状**：子代理频繁反馈信息不足，无法继续

**根因**：`implementer-prompt.md` 上下文不足，子代理没有拿到足够的技术细节

**解决方案**：
1. 检查 `openspec/changes/<name>/specs/` 内容是否足够详细
2. 检查 `openspec/changes/<name>/design.md` 是否覆盖接口定义和数据结构
3. 在 `openspec/config.yaml` 的 `context` 字段补充项目技术背景

---

## 推荐的人工干预节点

基于以上断裂点，在以下节点需要人工介入：

```
/opsx:propose
    ↓
[人工] 运行 writing-plans，对比两套任务，手动对齐
    ↓
/opsx:apply (subagent-driven-development)
    ↓
[人工] openspec status --json 确认真实进度
[人工] verification-before-completion 验证实际运行
    ↓
[人工] 手动将 delta specs 合并到 openspec/specs/
    ↓
/opsx:archive
```

---

## 参考资料

- 原文：https://cloud.tencent.com/developer/article/2664183
- OpenSpec 官方仓库：https://github.com/Fission-AI/OpenSpec
- Superpowers 官方仓库：https://github.com/obra/superpowers
