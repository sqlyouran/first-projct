## 1. 数据库与 Mock 数据

- [x] 1.1 创建 Flyway 迁移脚本 V3__community_schema.sql，新增 mock_users、posts、comments、post_hospital_tags、post_specialty_tags、user_interactions 表及索引
- [x] 1.2 创建 Flyway 迁移脚本 V4__community_seed_data.sql，预设 6 个 Mock 用户及若干示例帖子/评论数据

## 2. 后端 - Mock 用户

- [x] 2.1 创建 MockUser 实体类和 MockUserRepository
- [x] 2.2 创建 MockUserDto 和 MockUserController（GET /api/mock-users）
- [x] 2.3 编写 MockUserController 单元测试

## 3. 后端 - 帖子系统

- [x] 3.1 创建 Post 实体类（含 PostHospitalTag、PostSpecialtyTag 关联实体）和 PostRepository
- [x] 3.2 创建 PostDto（列表摘要）和 PostDetailDto（详情，含关联医院/专科信息）
- [x] 3.3 创建 PostService，实现帖子创建、分页列表（最新/最热排序）、详情查询、按医院/专科查关联帖子
- [x] 3.4 创建 PostController，暴露 REST API（GET/POST /api/posts, GET /api/posts/{id}, GET /api/posts/by-hospital/{id}, GET /api/posts/by-specialty/{id}）
- [x] 3.5 编写 PostController 单元测试（覆盖创建、列表排序、详情、404、按关联查询场景）

## 4. 后端 - 评论系统

- [x] 4.1 创建 Comment 实体类（含 parent_id 自引用）和 CommentRepository
- [x] 4.2 创建 CommentDto 和 CreateCommentRequest
- [x] 4.3 创建 CommentService，实现发表评论、回复评论（校验一层嵌套限制）、获取帖子评论列表（顶级评论+嵌套回复）
- [x] 4.4 创建 CommentController（GET/POST /api/posts/{id}/comments）
- [x] 4.5 编写 CommentController 单元测试（覆盖顶级评论、回复评论、嵌套层级限制、评论列表场景）

## 5. 后端 - 互动功能

- [x] 5.1 创建 UserInteraction 实体类和 UserInteractionRepository
- [x] 5.2 创建 InteractionService，实现帖子点赞/取消点赞、帖子收藏/取消收藏、评论点赞/取消点赞（toggle 逻辑 + 计数同步）
- [x] 5.3 创建 InteractionController（POST /api/posts/{id}/like, POST /api/posts/{id}/favorite, POST /api/comments/{id}/like）
- [x] 5.4 编写 InteractionController 单元测试（覆盖点赞、取消点赞、收藏、取消收藏场景）

## 6. 前端 - API 服务与类型定义

- [x] 6.1 在 types/index.ts 新增 MockUser、Post、PostDetail、Comment、CreatePostRequest、CreateCommentRequest 等类型定义
- [x] 6.2 在 services/api.ts 新增社区相关 API 调用函数（帖子 CRUD、评论、点赞/收藏、Mock 用户列表）

## 7. 前端 - 社区页面

- [x] 7.1 创建 CommunityPage 帖子列表页面（最新/最热切换、分页、帖子卡片展示）
- [x] 7.2 创建 PostDetailPage 帖子详情页面（完整内容、关联医院/专科标签、点赞/收藏按钮、评论区）
- [x] 7.3 创建 NewPostPage 发帖页面（标题、内容输入、Mock 用户选择、可选关联医院/专科）
- [x] 7.4 在 App.tsx 中添加 /community、/community/posts/:id、/community/new 路由
- [x] 7.5 在 Layout.tsx 导航栏新增"交流"入口

## 8. 前端 - 关联帖子展示

- [x] 8.1 修改 HospitalDetailPage，在详情下方展示该医院的关联帖子列表
- [x] 8.2 修改 SpecialtyRankingPage，在排名下方展示该专科的关联帖子列表
