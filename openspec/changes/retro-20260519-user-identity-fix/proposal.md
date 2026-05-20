## Why

当前社区交互层（点赞、收藏、评论）在 `frontend/src/pages/PostDetailPage.tsx:7` 硬编码 `MOCK_USER_ID = 1`，所有用户行为都被记录为用户 1。但 `NewPostPage.tsx:86-97` 已经提供了"Post as"用户切换器，意味着：用户可以以任意 mock 身份发帖，但回到详情页点赞/收藏永远是用户 1。这导致：

- **测试和演示不可行**：无法在浏览器中切换用户视角验证点赞/收藏的去重逻辑（mock_users 设计的初衷）
- **数据污染**：所有交互流向用户 1，统计数据失真
- **体验断裂**：用户在 NewPost 看到"作为 X 发帖"，然后下一秒所有动作变成用户 1，无任何提示

社区板块刚交付不久，趁现在用户量小修掉这个根因，避免后续基于错误数据做决策。

## What Changes

- 在 Layout 顶部增加一个全局 User Switcher 组件（下拉显示当前 mock 用户头像 + 昵称，可切换）
- 当前选中的 user 存到 localStorage 与 React Context，所有页面共享读取
- 移除 `PostDetailPage.tsx:7` 的 `MOCK_USER_ID = 1` 常量，改为从 Context 读取
- 移除 `NewPostPage.tsx:11,86-97` 的本地 userId state 与下拉，改为从 Context 读取（保持"以当前身份发帖"的语义）
- 新建 `frontend/src/contexts/CurrentUserContext.tsx` 与 `useCurrentUser` hook
- 在 Layout header 右侧（搜索框旁）展示"as <nickname>"小标签，点击弹出切换器

## Impact

- Affected specs: `mock-users`（新增"用户身份切换"requirement），`page-layouts`（新增 user switcher 在 header 中的位置）
- Affected code:
  - 新增：`frontend/src/contexts/CurrentUserContext.tsx`、`frontend/src/components/UserSwitcher.tsx`
  - 修改：`frontend/src/components/Layout.tsx`（接入 Provider 与 switcher）、`frontend/src/pages/PostDetailPage.tsx`、`frontend/src/pages/NewPostPage.tsx`、`frontend/src/main.tsx`（包裹 Provider）
- 后端无变更（API 已支持任意 userId）
- 数据迁移：可选 — 是否清空已有交互表中归属用户 1 的"伪数据"由用户决定

---
> 此 proposal 由 project-retrospective 自动生成（2026-05-19）。
> 请用户审阅后通过 `/opsx:propose retro-20260519-user-identity-fix` 完成 specs/design/tasks 生成。
