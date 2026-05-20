---
name: Retrospect
description: 召唤项目复盘官 — 主动发现项目问题与改进机会
category: Workflow
tags: [retrospective, review, planning]
---

调用 `project-retrospective` subagent 对项目进行全维度复盘，产出可执行的迭代建议与 propose 草稿。

**Input**: 可选 focus 参数（如 `/retrospect ui`、`/retrospect backend`、`/retrospect community`）。如未指定则全维度复盘。

**Steps**

1. **解析 focus 参数**

   从用户消息中提取 focus 关键词。如未提供，记为 `全维度`。

2. **检查 dev server 状态（决定是否补充截图证据）**

   ```bash
   curl -s -o /dev/null -w "%{http_code}" http://localhost:5173 2>&1
   curl -s -o /dev/null -w "%{http_code}" http://localhost:8080/actuator/health 2>&1 || curl -s -o /dev/null -w "%{http_code}" http://localhost:8080 2>&1
   ```

   - 两个端口都返回 200（或 frontend 200 即可）：进入步骤 3 收集截图
   - 否则：跳过截图，告知用户"dev server 未运行，本次复盘仅基于静态证据。如需运行时证据，请先启动服务后重试"

3. **（如 dev server 运行）调度 Browser subagent 收集截图**

   使用 Agent 工具，subagent_type 为 `Browser`，prompt 为：

   > 访问以下 4 个页面，每个页面截一张完整可视区域截图，保存到 `/tmp/retrospect-screenshots/`，并返回 4 个截图绝对路径：
   > 1. http://localhost:5173/
   > 2. http://localhost:5173/community
   > 3. http://localhost:5173/community/new
   > 4. http://localhost:5173/specialty/<任选一个有数据的科室 slug>
   >
   > 如某个页面访问失败，返回该 URL 的错误信息但继续完成其他截图。

4. **Dispatch project-retrospective subagent**

   调用 `project-retrospective` subagent，传入：
   - 用户的 focus 参数（步骤 1）
   - 截图路径列表（步骤 3 返回的，如有）
   - 项目根目录：`/Users/xx/Downloads/code/tmx/first`

   完整 prompt 模板：

   ```
   请对 first-projct 项目进行复盘。

   Focus: <focus 值>

   运行时截图（按页面顺序）：
   - 首页: <path1 或 "未提供">
   - 社区列表: <path2 或 "未提供">
   - 新建帖子: <path3 或 "未提供">
   - 科室排名: <path4 或 "未提供">

   请按你的标准工作流执行：收集证据 → 5 维度分析 → 产出报告 → 生成 propose 草稿。
   ```

5. **展示 subagent 返回的报告**

   subagent 会返回：
   - 完整的复盘报告（markdown）
   - 已生成的 propose 草稿路径列表

   把报告原样转发给用户，并在结尾追加：

   ```
   ---
   📌 接下来你可以：
   - 检查上面列出的 propose 草稿，对感兴趣的项目运行 `/opsx:propose <change-name>` 完整生成 specs/design/tasks
   - 对推荐"先 explore"的项目运行 `/opsx:explore "想法描述"` 进一步讨论
   - 直接告诉我哪些建议你想采纳/拒绝
   ```

**Guardrails**
- 永远不要直接修改业务代码（frontend/backend 源码）
- 永远不要直接代用户运行 `/opsx:propose`、`/opsx:apply`
- 如 dev server 未运行，不要静默跳过截图，必须明确告知用户
- 如 Browser subagent 截图失败，仍可继续复盘，只在报告里标注该维度证据缺失
- 复盘是 read-only + 草稿生成，不应改动现有 specs 或归档内容
