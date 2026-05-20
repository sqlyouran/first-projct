## 1. Backend — Inquiry Model & Migration

- [x] 1.1 Create Inquiry entity (id, userId, hospitalId, name, email, conditionSummary, preferredDate, status, createdAt)
- [x] 1.2 Create V7__inquiry_schema.sql (or V9 if patient-story uses V7/V8): CREATE TABLE inquiries with foreign keys to users and hospitals
- [x] 1.3 Create InquiryRepository (JpaRepository)
- [x] 1.4 Create InquiryService (createInquiry, getMyInquiries)
- [x] 1.5 Create InquiryController: POST /api/inquiries (authenticated), GET /api/users/me/inquiries (authenticated)
- [x] 1.6 Create CreateInquiryRequest DTO with validation (@NotBlank name, @Email email, @NotBlank conditionSummary)
- [x] 1.7 Create InquiryDto response DTO (include hospitalName from join)
- [x] 1.8 Add /api/inquiries permitAll for POST in SecurityConfig (or require auth — decision: require auth)

## 2. Frontend — Types & API

- [x] 2.1 Add Inquiry/CreateInquiryRequest types in types/index.ts
- [x] 2.2 Add createInquiry() and fetchMyInquiries() functions in api.ts

## 3. Frontend — InquiryFormModal

- [x] 3.1 Create InquiryFormModal.tsx: modal with name, email (pre-filled from auth), condition summary textarea, preferred date picker
- [x] 3.2 Show hospital name in modal header
- [x] 3.3 On success: show confirmation message with "We typically respond within 2 business days"
- [x] 3.4 Use design-system tokens (rounded-2xl, shadow-card, bg-primary CTA)

## 4. Frontend — Hospital Detail Page CTA

- [x] 4.1 Add "Send Inquiry" button to HospitalDetailPage action area
- [x] 4.2 On click: open InquiryFormModal (pass hospital id and name)
- [x] 4.3 Read URL param ?inquiry=open on mount → auto-open modal if present, then replaceState to remove param

## 5. Frontend — Specialty Ranking Page

- [x] 5.1 Add "Inquire" mini-button to each hospital row in SpecialtyRankingPage
- [x] 5.2 On click: navigate to /hospitals/{id}?inquiry=open

## 6. Frontend — My Inquiries Page

- [x] 6.1 Create MyInquiriesPage.tsx: list of user's inquiries with hospital name, condition, date, status badge
- [x] 6.2 Add route /my-inquiries in App.tsx wrapped with ProtectedRoute
- [x] 6.3 Add "My Inquiries" link in user profile dropdown (Layout.tsx)
- [x] 6.4 Show empty state when no inquiries

## 7. Verification

- [x] 7.1 Backend: `./mvnw test` passes
- [x] 7.2 Frontend: `npm run build` passes
- [x] 7.3 Start services and verify: Send Inquiry on hospital detail, Inquire button on ranking, My Inquiries page
