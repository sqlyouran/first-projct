## ADDED Requirements

### Requirement: Auth pages use design-system tokens
All authentication pages (Login, Register, Forgot Password, Reset Password) SHALL use the project's design-system color tokens, typography, border-radius, and shadow patterns instead of raw Tailwind utility classes.

#### Scenario: Auth page background matches main site
- **WHEN** a user navigates to /login, /register, /forgot-password, or /reset-password
- **THEN** the page background SHALL use `bg-background` (warm off-white #faf9f7) instead of `bg-gray-50`

#### Scenario: Auth card uses design-system pattern
- **WHEN** the authentication form card is rendered
- **THEN** it SHALL use `rounded-2xl shadow-card border-border` instead of `rounded-xl border-gray-100`

#### Scenario: Auth CTA button uses primary token
- **WHEN** the submit button (Sign In / Sign Up / Send / Reset) is rendered
- **THEN** it SHALL use `bg-primary hover:bg-primary-hover` instead of `bg-teal-600 hover:bg-teal-700`

#### Scenario: Auth input focus uses primary ring
- **WHEN** a user focuses on any auth form input
- **THEN** the input SHALL show `focus:ring-primary/20 focus:border-primary` instead of `focus:ring-teal-500`

### Requirement: Auth pages have brand header
All authentication pages SHALL display a brand header with the application name and a link to navigate back to the homepage.

#### Scenario: Brand name visible on auth page
- **WHEN** a user views any auth page
- **THEN** a brand name ("ChinaMedGuide" or equivalent) SHALL be displayed above the form

#### Scenario: Navigate back to homepage
- **WHEN** a user clicks the brand name on an auth page
- **THEN** the user SHALL be navigated to the homepage (/)

## REMOVED Requirements

### Requirement: Mock user selector in NewPostPage
**Reason**: Replaced by real user authentication. Posts now use the authenticated user from JWT token.
**Migration**: Users must sign in to create posts. The `NewPostPage` is wrapped in `<ProtectedRoute>`.

### Requirement: Mock user API endpoint
**Reason**: MockUserController, MockUser model, MockUserRepository are dead code after V5 migration.
**Migration**: All mock_users data was migrated to users table in V5. Frontend fetchMockUsers() and backend /api/mock-users endpoint SHALL be removed.
