## Context

Expert Team 实现了完整用户系统（JWT 认证、注册/登录、个人中心、Admin API），但遗留两类问题：
1. **视觉割裂**：LoginPage/RegisterPage/ForgotPasswordPage/ResetPasswordPage 在 `<Layout />` 外部渲染，使用原生 Tailwind class（gray-50, rounded-xl, border-gray-100），与主站 Warm Professional design-system tokens 不一致
2. **死代码**：MockUserController（@Deprecated）、MockUser model、MockUserRepository、前端 fetchMockUsers() 仍存在，V5 migration 已完成数据迁移

## Goals / Non-Goals

**Goals:**
- Auth 页面视觉与主站 design-system 对齐（bg-background, rounded-2xl, shadow-card, text-text-primary）
- Auth 页面有品牌标识（logo/名称 + 回首页链接），避免用户进入登录页后感觉"换了一个产品"
- 彻底清除 MockUser 相关代码（Controller、Model、Repository、DTO、前端 API、SecurityConfig 规则）
- Flyway V6 DROP TABLE mock_users

**Non-Goals:**
- 不改 Auth 逻辑（JWT、token 刷新、密码重置流程均保持不变）
- 不做 Auth 页面的 Layout 嵌套（保持独立页面布局，但复用 tokens）
- 不做 Admin 前端（另一个 change）
- 不处理 CORS / JWT Secret 问题（P0 热修，不走 propose）

## Decisions

### D1: Auth 页面不嵌入 Layout，但复用 design tokens
**Rationale**: Auth 页面作为独立全屏页面是业界惯例（Google、GitHub 均如此）。问题不在结构，而在没用 design tokens。
**做法**: 把 `bg-gray-50` 换成 `bg-background`，`rounded-xl` 换成 `rounded-2xl`，`border-gray-100` 换成 `border-border`，CTA 按钮用 `bg-primary hover:bg-primary-hover`。

### D2: MockUser 一次性全删，V6 migration DROP TABLE
**Rationale**: V5 已经把 mock_users 数据迁入 users 表（带密码 hash），mock_users 表无任何引用者。
**Alternative**: 保留表但标废弃 → 拒绝（增加认知负担且无回退需求）。

### D3: MockUserControllerTest 同步删除
**Rationale**: 测试对象被删除，测试必须跟着删。已有 AuthControllerTest 覆盖新认证流程。

## Risks / Trade-offs

- **[Low risk]** Auth 页面改样式可能影响表单 focus ring 颜色 → Mitigation: 统一使用 `focus:ring-primary/20 focus:border-primary`
- **[Trivial risk]** DROP mock_users 不可逆 → Mitigation: 数据已完整迁入 users 表，且 H2 是内存库，每次启动重建
