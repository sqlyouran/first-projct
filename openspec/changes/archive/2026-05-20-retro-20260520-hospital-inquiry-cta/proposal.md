## Why

从 `HospitalDetailPage.tsx`（`frontend/src/pages/HospitalDetailPage.tsx:35-134`）可以看到：用户浏览完医院详情后，唯一的行动出口是 "Visit Website"（跳转到医院官网，大概率是纯中文页面）。没有任何方式让用户迈出"我想去这家医院"的下一步。

这是当前产品最大的断点：**信息查询 → 决策 → ??? → 就医**。中间的"行动"环节完全空白。

对于来中国看病的海外用户，"下一步"可能是：
- 想要咨询：能否发一封英文邮件/消息给医院国际部？
- 想要预估费用：这个手术大概多少钱？
- 想要了解流程：需要什么签证？能住多久？

当前所有医院页面（见 `/tmp/specialty.png` 排名表）只展示静态排名数据，没有任何 Call-to-Action。用户看完就走，转化率为零。

从 `Hospital` model（`backend/src/main/java/com/firstprojct/model/Hospital.java`）看到已有 `phone`、`website` 字段，但前端展示非常克制，缺乏引导用户主动发起联系的设计。

## What Changes

- 在 `HospitalDetailPage` 顶部增加醒目的 CTA 区域：
  - "Send Inquiry" 按钮：填写简单表单（姓名、邮箱、病症摘要、期望时间），系统记录询诊请求
  - "View Cost Guide" 链接：跳转到该科室的费用参考区域
  - "Call International Dept" 按钮：对有国际部的医院直接展示电话（click-to-call）
- 在科室排名页（`SpecialtyRankingPage`）的每行医院名旁增加 "Inquire" mini-button
- 后端新增 `Inquiry` model 与 `InquiryController`，支持提交和查看自己的询诊记录
- 用户个人中心新增 "My Inquiries" 页面
- （MVP 版本不对接医院系统，仅记录到数据库，后续可做邮件转发或对接）

## Impact

- Affected specs: `page-layouts`（医院详情页增加 CTA 区域）、新增 `inquiry` capability spec
- Affected code:
  - 后端：新增 `Inquiry` model、`InquiryRepository`、`InquiryService`、`InquiryController`；新增 `V7__inquiry_schema.sql`
  - 前端：修改 `HospitalDetailPage.tsx`（增加 CTA 区域）、修改 `SpecialtyRankingPage.tsx`（增加行内 Inquire 按钮）、新增 `InquiryFormModal.tsx`、新增 `MyInquiriesPage.tsx`

---
> 此 proposal 由 project-retrospective 自动生成（2026-05-20）。
> 请用户审阅后通过 `/opsx:propose retro-20260520-hospital-inquiry-cta` 完成 specs/design/tasks 生成。
