## 1. Backend — Post Model & Migration

- [x] 1.1 Add structured fields to Post entity: type (VARCHAR, default 'DISCUSSION'), condition (VARCHAR), treatmentType (VARCHAR), costRange (VARCHAR), timelineDays (INTEGER), outcome (VARCHAR), nationality (VARCHAR)
- [x] 1.2 Create V7__patient_story_fields.sql: ALTER TABLE posts ADD COLUMN for each new field
- [x] 1.3 Update CreatePostRequest DTO to accept new fields
- [x] 1.4 Update PostService to support type query parameter in fetchPosts
- [x] 1.5 Update PostController GET /api/posts to accept optional `type` filter param
- [x] 1.6 Update Post/PostDetail response DTOs to include structured fields

## 2. Backend — English Seed Data

- [x] 2.1 Create V8__english_story_seed.sql with 5 English Patient Story posts and 3 English Discussion posts (diverse specialties: orthopedics, cardiology, oncology, neurology, ophthalmology)

## 3. Frontend — Types & API

- [x] 3.1 Update Post/PostDetail types in types/index.ts with structured fields (type, condition, treatmentType, costRange, timelineDays, outcome, nationality)
- [x] 3.2 Update fetchPosts() in api.ts to accept optional `type` param
- [x] 3.3 Add createStory() function in api.ts (calls createPost with type=STORY + structured fields)

## 4. Frontend — Story Card Component

- [x] 4.1 Create StoryCard.tsx component with badge-style indicators for cost range, outcome, timeline
- [x] 4.2 Use design-system tokens (bg-primary/10, text-primary, rounded-xl badges)

## 5. Frontend — Community Page Stories Tab

- [x] 5.1 Add "Stories" tab to CommunityPage alongside Latest/Hot
- [x] 5.2 Add "Share Your Story" CTA button next to "New Post" (navigates to /community/new-story)
- [x] 5.3 When Stories tab active, fetch posts with type=STORY and render using StoryCard

## 6. Frontend — New Story Page

- [x] 6.1 Create NewStoryPage.tsx with structured form: condition input, treatmentType select, costRange dropdown, timelineDays input, outcome dropdown, nationality input, plus title & content
- [x] 6.2 Add route /community/new-story in App.tsx wrapped with ProtectedRoute
- [x] 6.3 On submit, call createStory API and navigate to the created story detail

## 7. Frontend — Homepage Featured Stories

- [x] 7.1 Add Featured Stories section to HomePage below hero area
- [x] 7.2 Fetch top 3 stories (type=STORY, sort=hot, size=3)
- [x] 7.3 Render using StoryCard components in a grid/row layout
- [x] 7.4 If no stories exist, hide the section entirely

## 8. Frontend — Post Detail Enhancements

- [x] 8.1 Update PostDetailPage to display structured fields (condition, treatment, cost, timeline, outcome, nationality) as a summary card at the top for STORY type posts

## 9. Verification

- [x] 9.1 Backend: `./mvnw test` passes
- [x] 9.2 Frontend: `npm run build` passes
- [x] 9.3 Start services and verify: Stories tab, New Story form, Featured Stories on homepage, Story detail page with structured fields
