## Why

用户系统已由 Expert Team 实现，但存在体验割裂和技术债残留：

1. **Auth 页面视觉断层**：登录/注册页面在 `<Layout />` 之外，使用 gray-50/rounded-xl/border-gray-100 等非设计系统 token，与主站 Warm Professional 风格完全脱节。用户点击 "Sign In" 后感觉切换到了另一个产品。
2. **MockUser 残留**：MockUserController（@Deprecated）、MockUser model、MockUserRepository、前端 fetchMockUsers() 仍存在，V5 migration 已将数据迁入 users 表，这些是纯粹的死代码。
3. **SecurityConfig permitAll `/api/mock-users`** 仍在白名单中。

## What Changes

- 重写 LoginPage / RegisterPage / ForgotPasswordPage / ResetPasswordPage 使用 design-system tokens（bg-background, rounded-2xl, shadow-card, text-text-primary 等）
- Auth 页面加品牌 header（logo + 回首页链接）
- 删除 MockUserController.java、MockUser.java、MockUserRepository.java、MockUserDto.java
- 删除前端 fetchMockUsers() 函数及其类型引用
- SecurityConfig 移除 `/api/mock-users` permitAll 规则
- 添加 Flyway V6 DROP TABLE mock_users

## Impact

- Affected specs: `design-system`, `page-layouts`
- Affected code:
  - `frontend/src/pages/LoginPage.tsx`, `RegisterPage.tsx`, `ForgotPasswordPage.tsx`, `ResetPasswordPage.tsx`
  - `frontend/src/services/api.ts`（删除 fetchMockUsers）
  - `backend/src/main/java/com/firstprojct/controller/MockUserController.java`（删除）
  - `backend/src/main/java/com/firstprojct/model/MockUser.java`（删除）
  - `backend/src/main/java/com/firstprojct/repository/MockUserRepository.java`（删除）
  - `backend/src/main/java/com/firstprojct/config/SecurityConfig.java`（移除规则）
  - `backend/src/main/resources/db/migration/V6__drop_mock_users.sql`（新增）

---
> 此 proposal 由 project-retrospective 自动生成（2026-05-20）。
> 请用户审阅后通过 `/opsx:propose retro-20260520-auth-polish-cleanup` 完成 specs/design/tasks 生成。
