# OpenSpec CLI 使用手册

> 版本：@fission-ai/openspec@1.3.1 | 更新日期：2026-05-19

## 全局命令速查

| 命令 | 作用 |
|------|------|
| `openspec init` | 在项目中初始化 OpenSpec |
| `openspec update` | 更新 AI 指令文件（升级后跑一次） |
| `openspec list` | 列出所有活跃的 change |
| `openspec list --specs` | 列出所有 spec |
| `openspec view` | 打开交互式仪表盘 |
| `openspec schemas` | 查看可用的工作流 schema |
| `openspec validate --all` | 校验所有 change 和 spec |
| `openspec show <name>` | 查看某个 change/spec 的内容 |

---

## 完整工作流（4步）

### 第1步：创建 Change

```bash
# 基础用法
openspec new change add-user-auth

# 带描述
openspec new change add-user-auth --description "用户认证模块"

# 指定 schema（默认 spec-driven）
openspec new change add-user-auth --schema spec-driven
```

生成的目录结构：

```
openspec/changes/add-user-auth/
├── .openspec.yaml     ← 自动生成，记录 schema 和 artifact 状态
└── README.md          ← 包含描述信息
```

### 第2步：生成 Artifacts（提案 → 规格 → 设计 → 任务）

```bash
# 查看当前 change 状态，了解需要生成哪些 artifact
openspec status --change add-user-auth

# 获取某个 artifact 的创建指引（AI 读取后按模板生成）
openspec instructions proposal --change add-user-auth --json
openspec instructions specs --change add-user-auth --json
openspec instructions design --change add-user-auth --json
openspec instructions tasks --change add-user-auth --json

# 获取 apply 阶段的指引（包含任务列表和上下文文件）
openspec instructions apply --change add-user-auth --json
```

Artifact 生成顺序（spec-driven schema）：

```
proposal → specs → design → tasks
   ↑          ↑        ↑       ↑
   │          │        │       └─ 最后：实施任务清单
   │          │        └─ 技术方案设计
   │          └─ 需求规格（能力 + 场景）
   └─ 提案：做什么 & 为什么
```

每个 `instructions` 命令返回 JSON 包含：

| 字段 | 说明 |
|------|------|
| `context` | 项目背景（约束，不写入文件） |
| `rules` | artifact 规则（约束，不写入文件） |
| `template` | 输出文件结构模板 |
| `instruction` | 该 artifact 的写作指引 |
| `outputPath` | 文件写入路径 |
| `dependencies` | 需要先读取的依赖 artifact |

### 第3步：实施（Apply）

```bash
# 查看任务进度
openspec status --change add-user-auth

# 获取实施指引
openspec instructions apply --change add-user-auth --json
```

返回的内容包括：任务列表、完成进度、上下文文件路径。AI 据此逐条实施。

### 第4步：归档（Archive）

```bash
# 交互式归档（有确认提示）
openspec archive add-user-auth

# 跳过确认直接归档
openspec archive add-user-auth -y

# 跳过 spec 同步（纯代码/工具类变更）
openspec archive add-user-auth --skip-specs
```

归档后目录变化：

```
openspec/changes/add-user-auth/
    → openspec/changes/archive/2026-05-19-add-user-auth/
```

---

## 配置管理

```bash
# 查看当前配置
openspec config list

# 切换工作流 profile（core / extended）
openspec config profile
#   core:     propose, explore, apply, archive
#   extended: 额外支持 /opsx:new, /opsx:continue, /opsx:ff,
#             /opsx:verify, /opsx:bulk-archive, /opsx:onboard

# 设置/读取单个配置项
openspec config get profile
openspec config set profile extended

# 编辑配置文件
openspec config edit

# 重置配置
openspec config reset
```

当前项目配置：

```
profile: core
delivery: both
workflows: propose, explore, apply, archive
```

切换到扩展 profile：

```bash
openspec config profile extended
openspec update    # 刷新指令文件
```

---

## 校验 & 调试

```bash
# 校验单个 change
openspec validate add-user-auth

# 校验所有 change
openspec validate --changes

# 校验所有 spec
openspec validate --specs

# 严格模式（更详细检查）
openspec validate --all --strict

# JSON 输出（脚本化使用）
openspec validate add-user-auth --json
```

---

## 斜杠命令（在 AI 终端中使用）

### Core Profile 命令

| 命令 | 对应 CLI 操作 |
|------|-------------|
| `/opsx:explore "idea"` | 进入探索模式，自由思考需求 |
| `/opsx:propose "name"` | 一步完成：创建 change + 生成全部 artifact |
| `/opsx:apply` | 按 tasks.md 逐条实施 |
| `/opsx:archive` | 归档完成的 change |

### Extended Profile 额外命令

| 命令 | 作用 |
|------|------|
| `/opsx:new` | 逐步创建 change（非一步到位） |
| `/opsx:continue` | 继续未完成的 artifact |
| `/opsx:ff` | 快进跳过已完成步骤 |
| `/opsx:verify` | 验证实施结果 |
| `/opsx:bulk-archive` | 批量归档 |
| `/opsx:onboard` | 项目上手引导 |

---

## 典型使用场景

### 场景1：快速提案（推荐）

```bash
# 在 Qoder/Claude 中直接输入
/opsx:propose "商家claw MVP异常订单巡检"
# → 自动创建 change + 生成 proposal/specs/design/tasks
# → 确认后执行
/opsx:apply
# → 完成后归档
/opsx:archive
```

### 场景2：手动逐步走

```bash
openspec new change merchant-claw
openspec instructions proposal --change merchant-claw --json
# AI 根据指引生成 proposal.md
openspec status --change merchant-claw
# 继续下一个 artifact...
openspec instructions specs --change merchant-claw --json
# 依次直到 tasks.md 完成
```

### 场景3：纯 CLI 查看

```bash
openspec list                  # 看当前有哪些活跃 change
openspec show merchant-claw    # 看某个 change 的完整内容
openspec status --change merchant-claw  # 看 artifact 完成度
openspec view                  # 打开交互式仪表盘
```

---

## 安装 & 升级

```bash
# 安装（需要 Node.js 20.19+）
npm install -g @fission-ai/openspec@latest

# 升级
npm install -g @fission-ai/openspec@latest

# 升级后在项目内刷新指令文件
openspec update

# 验证版本
openspec --version
```

---

## 参考资料

- 官方仓库：https://github.com/Fission-AI/OpenSpec
- 快速入门：https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md
- 命令文档：https://github.com/Fission-AI/OpenSpec/blob/main/docs/commands.md
- CLI 参考：https://github.com/Fission-AI/OpenSpec/blob/main/docs/cli.md
