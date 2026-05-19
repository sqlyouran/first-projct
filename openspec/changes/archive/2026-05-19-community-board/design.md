## Context

当前系统是一个医疗目录应用，包含医院（hospitals）、专科（specialties）和排名（specialty_rankings）三个核心实体。系统为纯浏览型，无用户体系、无 UGC 内容。

本次新增"交流板块"，引入 UGC 内容体系（帖子、评论、互动），并通过可选的关联关系与现有医院/专科实体连接。用户系统暂用 Mock 数据，后续再接入真实注册/登录。

技术栈：后端 Spring Boot 3.4 + JPA + H2，前端 React 19 + TypeScript + Vite。

## Goals / Non-Goals

**Goals:**
- 实现完整的帖子发布、浏览、详情查看功能
- 帖子可选关联多个医院和/或专科（多对多，不强制）
- 评论支持一层嵌套回复
- 支持点赞（帖子+评论）和收藏（帖子）
- 帖子列表支持最新/最热排序
- 前端新增社区一级入口，医院/专科详情页展示关联帖子
- 使用 Mock 用户数据，为后续用户系统预留扩展性

**Non-Goals:**
- 用户注册/登录系统（后置）
- 内容审核/举报机制（后置）
- 关注用户功能（后置）
- 富文本编辑器（MVP 使用纯文本）
- 图片上传（后置）

## Decisions

### 1. 数据模型设计

**决策**：新增 5 张表 — `mock_users`、`posts`、`comments`、`post_hospital_tags`、`post_specialty_tags`、`user_interactions`

**理由**：
- `mock_users` 独立于 posts，便于后续替换为真实用户表，只需改 FK 指向
- 帖子与医院/专科采用两张关联表（`post_hospital_tags`、`post_specialty_tags`），而非通用 `post_tags` + type 字段，因为两类实体有不同的 FK 约束，分表更清晰
- `user_interactions` 使用 `target_type`（POST/COMMENT）+ `target_id` 的多态设计，避免为每种互动建单独表
- 使用联合唯一约束防止重复点赞/收藏

**备选方案**：
- 通用 `post_tags(post_id, entity_type, entity_id)` 单表 — 更灵活但失去 FK 约束保护，放弃
- 点赞/收藏分别建表 — 更规范但表数量膨胀，MVP 阶段不必要，放弃

### 2. 评论层级方案

**决策**：评论表使用 `parent_id` 自引用，但业务层限制只允许一层嵌套（即回复只能回复顶级评论）

**理由**：
- 数据结构上预留了无限嵌套的可能（`parent_id` 自引用）
- 业务层校验 `parent_id` 指向的评论其 `parent_id` 必须为 null，保证只有一层
- 前端展示简单清晰，移动端友好

### 3. "最热"排序策略

**决策**：基于 `like_count + comment_count` 降序排列，不引入时间衰减因子

**理由**：MVP 阶段数据量小，简单聚合排序足够。后续数据量增长后可引入 Wilson Score 或时间衰减算法。

### 4. API 设计

**决策**：RESTful 风格，以 `/api/posts` 为根路径

| 端点 | 方法 | 说明 |
|------|------|------|
| `/api/posts` | GET | 帖子列表（分页 + 排序） |
| `/api/posts` | POST | 创建帖子 |
| `/api/posts/{id}` | GET | 帖子详情 |
| `/api/posts/{id}/comments` | GET | 获取帖子评论 |
| `/api/posts/{id}/comments` | POST | 发表评论/回复 |
| `/api/posts/{id}/like` | POST | 点赞/取消点赞帖子 |
| `/api/posts/{id}/favorite` | POST | 收藏/取消收藏帖子 |
| `/api/comments/{id}/like` | POST | 点赞/取消点赞评论 |
| `/api/posts/by-hospital/{hospitalId}` | GET | 按医院查关联帖子 |
| `/api/posts/by-specialty/{specialtyId}` | GET | 按专科查关联帖子 |
| `/api/mock-users` | GET | 获取 Mock 用户列表（供前端发帖时选择） |

### 5. Mock 用户策略

**决策**：通过 Flyway seed 脚本预设 5-8 个 Mock 用户，前端发帖时提供用户选择下拉框

**理由**：最小化实现成本，不需要 session/token 机制。后续接入真实用户系统时，替换 `user_id` 来源即可。

## Risks / Trade-offs

- **[无真实用户校验]** → Mock 用户方案意味着任何人都可以伪装任何用户发帖。MVP 阶段可接受，上线前必须接入用户系统。
- **[无内容审核]** → 医疗场景内容敏感。迁移风险：如果先上线再补审核，可能出现不当内容。→ 缓解：MVP 阶段控制访问范围，不对外公开。
- **[多态互动表]** → `user_interactions` 的 `target_type + target_id` 无法使用数据库级 FK 约束。→ 缓解：应用层校验 + 数据清理策略。
- **[H2 内存数据库]** → 每次重启数据丢失。→ 缓解：Flyway seed 脚本预设演示数据，开发阶段可接受。
