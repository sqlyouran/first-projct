## Why

`frontend/src/components/Layout.tsx:24-26` 的导航链接是中文（"首页"、"交流"），但同一个 Layout 内的搜索框 placeholder（`Search hospitals...`）、Footer（`ChinaMedGuide / Helping international residents... / Hospital Rankings / Community / About`）、Hero 大标题（`Find Top Hospitals in China`）、CommunityPage / PostDetailPage / NewPostPage 全部 UI 文案都是英文。这是 UI 重设计阶段（`2026-05-19-ui-redesign-warm-professional`）遗留的最大体验断裂：

- 产品定位明确写在 Footer 上："Helping **international residents** find quality healthcare in China" — 即目标用户是在华外国人
- Warm Professional 设计方向也是为欧美用户做的
- 但导航这个最高频接触点用中文，第一眼就让外国用户认为 "This is a Chinese-only product"
- 中国用户来看这个页面也会困惑：为什么导航中文、内容英文？

文案不统一是几乎零成本可修但极大提升体验的问题，应当优先于任何新功能。

## What Changes

- 全局文案语言统一为英文（与目标用户一致）：
  - `Layout.tsx`：导航 `首页 → Home`、`交流 → Community`
  - 所有页面已经是英文，无需大改，但需要 grep 一遍残留中文
- 数据层中文保留（医院 nameCn、专科 nameCn、地址等是真实中文数据，不是 UI 文案，保留）
- 保留 specialty.icon 的 emoji（数据层）
- （可选）添加最简陋的 lang 切换占位（暂不实现 i18n，仅留出未来 hook，避免现在过度设计）

明确**不做**的事：
- 不引入 i18next 等 i18n 库（MVP 阶段过度工程）
- 不做中文版本（产品定位决定）
- 不改后端字段（nameCn 等保留）

## Impact

- Affected specs: `page-layouts`（更新 navigation links requirement，明确英文）
- Affected code:
  - `frontend/src/components/Layout.tsx`（line 24-26 改 nav labels）
  - 全局 grep `[\u4e00-\u9fff]` 排查 UI 中残留的中文常量字符串
  - `frontend/index.html` 的 `<html lang="zh-CN">` 改为 `lang="en"`
- 后端无变更
- 数据无变更（医院/专科中文名称属于内容，保留）

---
> 此 proposal 由 project-retrospective 自动生成（2026-05-19）。
> 请用户审阅后通过 `/opsx:propose retro-20260519-unify-ui-english` 完成 specs/design/tasks 生成。
