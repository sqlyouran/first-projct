## Context

HospitalDetailPage 展示医院信息（名称、地址、电话、描述、优势科室），唯一 CTA 是 "Visit Website" 跳转到大概率纯中文的医院官网。SpecialtyRankingPage 排名表纯展示，无行动入口。用户完成"信息查询→决策"后无路可走，转化率为零。

Hospital model 已有 phone/website 字段。需新增 Inquiry 模型，让用户提交询诊请求，系统先存储再后续对接。

## Goals / Non-Goals

**Goals:**
- 新增 Inquiry 模型（name, email, conditionSummary, preferredDate, hospitalId, userId, status）
- HospitalDetailPage 增加 "Send Inquiry" CTA，弹出 Modal 表单
- SpecialtyRankingPage 排名行增加 "Inquire" mini 按钮
- 用户个人中心增加 "My Inquiries" 页面
- MVP 阶段仅存储询诊到数据库，不发邮件/不对接医院系统

**Non-Goals:**
- 不做邮件发送（后续迭代）
- 不做医院端后台（B 端功能）
- 不做在线支付/预约确认
- 不做 "View Cost Guide" 链接（费用模块需先 explore）

## Decisions

### D1: Inquiry 独立表 vs 扩展 Post
**选择**: 新建 inquiries 表（独立于 posts）
**Rationale**: Inquiry 是私密的一对一请求（name/email/condition），与社区公开帖子性质完全不同。独立表可以有自己的状态流（PENDING/CONTACTED/CLOSED），不污染社区数据。

### D2: InquiryFormModal vs 独立页面
**选择**: Modal 弹窗（在 HospitalDetailPage 上方弹出）
**Rationale**: 用户在浏览医院详情时，CTA 应该原地触发，不要跳转打断浏览上下文。表单字段少（4 个），Modal 足够承载。

### D3: 排名页的 Inquire 按钮行为
**选择**: 点击后跳转到对应 HospitalDetailPage 并自动打开 Inquiry Modal
**Rationale**: 排名页空间有限不适合内嵌表单，跳转到详情页保持体验一致。通过 URL query param `?inquiry=open` 触发自动弹出。

### D4: Inquiry 状态机
**选择**: 三态枚举 PENDING → CONTACTED → CLOSED
**Rationale**: MVP 阶段只需让用户看到"已提交"状态。CONTACTED/CLOSED 预留给后续管理端手动更新。

## Risks / Trade-offs

- **[Low risk]** Inquiry 表无邮件通知，用户提交后只能被动等待 → Mitigation: 提交成功后显示预计回复时间提示（"We typically respond within 2 business days"）
- **[Low risk]** 排名页跳转+自动弹窗依赖 URL param，刷新可能重复弹出 → Mitigation: 弹窗打开后用 replaceState 移除 query param
