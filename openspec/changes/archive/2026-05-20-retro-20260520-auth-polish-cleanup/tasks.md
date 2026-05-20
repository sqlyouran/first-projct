## 1. Auth Pages Visual Alignment

- [x] 1.1 Update LoginPage.tsx: replace gray-50/rounded-xl/border-gray-100/teal-600 with design-system tokens (bg-background, rounded-2xl, shadow-card, border-border, bg-primary)
- [x] 1.2 Update RegisterPage.tsx: same token replacements as LoginPage
- [x] 1.3 Update ForgotPasswordPage.tsx: same token replacements
- [x] 1.4 Update ResetPasswordPage.tsx: same token replacements
- [x] 1.5 Add brand header (logo + homepage link) to all 4 auth pages

## 2. MockUser Cleanup - Backend

- [x] 2.1 Delete MockUserController.java
- [x] 2.2 Delete MockUser.java model
- [x] 2.3 Delete MockUserRepository.java
- [x] 2.4 Delete MockUserDto.java
- [x] 2.5 Remove `/api/mock-users` permitAll rule from SecurityConfig.java
- [x] 2.6 Delete MockUserControllerTest.java

## 3. MockUser Cleanup - Frontend

- [x] 3.1 Remove fetchMockUsers() from api.ts
- [x] 3.2 Remove MockUser type from types/index.ts
- [x] 3.3 Verify no remaining references to mock-user in frontend source

## 4. Database Migration

- [x] 4.1 Create V6__drop_mock_users.sql to DROP TABLE mock_users

## 5. Verification

- [x] 5.1 Backend: `./mvnw test` passes
- [x] 5.2 Frontend: `npm run build` passes
- [x] 5.3 Start services and verify login/register pages render with new styles
