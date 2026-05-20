## Why

当前社区板块是一个通用论坛（发帖 + 评论 + 点赞），缺乏结构化的患者就医故事。从运行时截图（`/tmp/community.png`）可以看到，社区 seed 数据全部是中文帖子（"甲状腺结节不要慌"、"陪诊员眼中的好医院标准"），与产品面向国际用户的定位严重矛盾。

更关键的是：海外用户决定来中国看病，最核心的决策依据是**真实患者的完整就医经历** —— 不是零散的论坛帖子，而是带有结构化信息（病症、医院、医生、费用、时间线、治疗结果）的 Patient Journey Story。这类内容：

- **是决策利器**：让潜在患者看到"和我同样病症的人在这家医院花了 $X，恢复了 Y 天"
- **是传播爆点**：一个结构化的"我在中国省了 $50,000 做了膝关节置换"故事，可以在 Reddit、Facebook 医疗社群引发大量转发
- **是 SEO 金矿**：长尾搜索如 "hip replacement China cost experience" 直接命中
- **是黏性引擎**：写过故事的用户有归属感，会持续回来更新恢复进展、回答新患者提问

当前 `community-posts` spec（`openspec/specs/community-posts/spec.md`）只支持纯文本标题 + 内容，无结构化字段。`PostDetail` 类型（`frontend/src/types/index.ts:71-83`）也只有 title/content/hospitals/specialties，无法承载就医故事需要的丰富信息。

## What Changes

- 新增 "Patient Story" 帖子类型（区别于普通 Discussion 帖子），包含结构化字段：
  - 病症/诊断（Condition）
  - 就诊医院（已有关联）+ 就诊科室（已有关联）
  - 治疗方式（Treatment type: surgery / medication / consultation etc.）
  - 费用范围（Cost range: 选择区间而非精确数字，降低隐私顾虑）
  - 就医时间线（Timeline: 从第一次联系到完成治疗的天数）
  - 治疗结果自评（Outcome: excellent / good / fair / poor）
  - 国籍/来源国（可选，用于同胞参考）
  - 自由叙述（保留现有的正文内容字段）
- 在首页 Hero 区域下方增加 "Featured Patient Stories" 轮播（替代或补充纯科室列表的单调感）
- 社区页面增加 "Stories" tab（与 Latest / Hot 并列），专门展示结构化故事
- 故事卡片设计突出关键数据：费用 / 结果 / 时间线，一眼可扫
- 故事详情页增加 "Share This Story" 按钮（生成包含关键数据的分享卡片图）

## Impact

- Affected specs: `community-posts`（新增 story 帖子类型与结构化字段）、`page-layouts`（首页增加 featured stories 区域、社区页增加 Stories tab）
- Affected code:
  - 后端：`Post` model 新增结构化字段、新建 `V7__patient_story_fields.sql` 迁移、`PostService` 支持按类型查询
  - 前端：新增 `NewStoryPage.tsx`（结构化表单）、修改 `CommunityPage.tsx`（增加 Stories tab）、修改 `HomePage.tsx`（增加 Featured Stories）、新增 `StoryCard` 组件
- 数据迁移：新增英文 seed 数据替换/补充现有中文 seed 帖子

---
> 此 proposal 由 project-retrospective 自动生成（2026-05-20）。
> 请用户审阅后通过 `/opsx:propose retro-20260520-patient-journey-stories` 完成 specs/design/tasks 生成。
