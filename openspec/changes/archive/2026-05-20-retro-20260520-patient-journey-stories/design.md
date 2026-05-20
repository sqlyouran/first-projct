## Context

当前社区为通用论坛（title + content + hospitalIds + specialtyIds），需要新增 "Patient Story" 结构化帖子类型。现有 `Post` model 使用 posts 表（V4 创建），前端通过 `CommunityPage` 展示列表、`PostDetailPage` 展示详情、`NewPostPage` 创建帖子。首页 `HomePage` 只展示科室卡片网格，无社区内容入口。

同时社区 seed 数据全中文，与产品面向国际用户定位矛盾，需替换为英文 Patient Story seed 数据。

## Goals / Non-Goals

**Goals:**
- Post model 增加可空的结构化字段（type, condition, treatmentType, costRange, timelineDays, outcome, nationality），通过 type 区分 DISCUSSION / STORY
- 新增 NewStoryPage（结构化表单），复用现有 createPost API 只扩展字段
- 社区页增加 Stories tab 筛选 type=STORY
- 首页 Hero 下方增加 Featured Stories 区域（最多 3 条高质量 story）
- 故事卡片突出展示费用/结果/时间线关键指标
- 英文 seed 数据（5-8 条 Patient Story + 2-3 条 Discussion）替换现有中文 seed

**Non-Goals:**
- 不做分享卡片图生成（后续迭代）
- 不做 AI 推荐/匹配（长期机会）
- 不改认证/权限逻辑
- 不做 i18n（内容层面用英文 seed 解决，不做框架级多语言）

## Decisions

### D1: Post 表加字段 vs 新建 story 表
**选择**: Post 表加可空字段 + type 枚举（DISCUSSION / STORY）
**Rationale**: Story 与 Discussion 共享 title/content/author/likes/comments 基础能力，只是额外多了结构化字段。单表 + type 判断比双表 JOIN 简单得多，且前端 PostDetail 组件可统一处理。
**Alternative**: 新建 patient_stories 表关联 posts → 拒绝（增加复杂度，N+1 查询风险）

### D2: costRange 使用枚举区间而非精确数字
**选择**: 存为 VARCHAR 枚举值（如 "UNDER_5K", "5K_10K", "10K_25K", "25K_50K", "OVER_50K"），单位固定 USD
**Rationale**: 精确费用涉及隐私和法律风险，枚举区间降低用户填写门槛且足够用于决策参考
**Alternative**: 存 int 精确金额 → 拒绝（隐私顾虑大，用户不愿填）

### D3: seed 数据策略 — 追加英文 + 保留部分中文
**选择**: V7 migration 新增英文 Patient Story seed（5 条）和英文 Discussion seed（3 条），同时保留现有中文 seed（模拟双语社区现实情况）
**Rationale**: 直接删除中文 seed 会导致 V4 migration 与 V7 矛盾。保留中文内容也符合"在中国生活的外国人社区"的真实场景
**Alternative**: 用 V7 DELETE 删除中文数据再 INSERT 英文 → 拒绝（破坏 seed 数据连续性）

### D4: Featured Stories API 用查询参数实现
**选择**: GET /api/posts?type=STORY&sort=hot&size=3，前端首页调用此接口
**Rationale**: 无需新增 endpoint，复用现有分页+排序逻辑

## Risks / Trade-offs

- **[Low risk]** Post 表加 6 个可空字段会使表宽度增加 → Mitigation: 全部可空，DISCUSSION 类型帖子这些字段为 null，不影响现有功能
- **[Medium risk]** 英文 seed 故事的医疗信息需标注 disclaimer → Mitigation: seed 数据开头加 "This is a community-shared experience, not medical advice" 声明
