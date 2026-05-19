# Community Board Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a patient community discussion board to the medical directory app with posts, comments, interactions (like/favorite), mock users, and frontend pages.

**Architecture:** Backend: Spring Boot 3.4 + JPA + H2 with RESTful APIs. Frontend: React 19 + TypeScript + Vite. New tables for mock_users, posts, comments, post_hospital_tags, post_specialty_tags, user_interactions. TDD approach: tests reference WHEN/THEN scenarios from OpenSpec delta specs.

**Tech Stack:** Java 17, Spring Boot 3.4, Spring Data JPA, H2, Flyway, React 19, TypeScript, Vite 6

---

## Codebase Patterns (MUST follow)

- **Entities**: Regular Java classes, manual getters/setters, `@Entity`/`@Table`/`@Column`, `GenerationType.IDENTITY`
- **DTOs**: Java `record` types
- **Repositories**: Extend `JpaRepository`, custom `@Query` as needed
- **Services**: `@Service`, constructor injection, return DTOs
- **Controllers**: `@RestController`, `@RequestMapping("/api/...")`, constructor injection, `ResponseEntity` for status control
- **Tests**: `@SpringBootTest` + `@AutoConfigureMockMvc`, use `MockMvc`, test against Flyway seed data
- **Frontend types**: TypeScript `interface`
- **Frontend API**: `fetchJson<T>()` utility in `services/api.ts`
- **Frontend pages**: Functional components with `useState`/`useEffect`, Tailwind CSS

---

### Task 1: Database Schema Migration (V3)

**Files:**
- Create: `backend/src/main/resources/db/migration/V3__community_schema.sql`

- [ ] **Step 1: Create V3 migration SQL**

Create tables: `mock_users` (id, nickname, avatar_url, created_at), `posts` (id, user_id FK→mock_users, title, content TEXT, like_count default 0, favorite_count default 0, comment_count default 0, created_at, updated_at), `comments` (id, post_id FK→posts, user_id FK→mock_users, parent_id FK→comments nullable, content TEXT, like_count default 0, created_at), `post_hospital_tags` (id, post_id FK→posts, hospital_id FK→hospitals, unique(post_id, hospital_id)), `post_specialty_tags` (id, post_id FK→posts, specialty_id FK→specialties, unique(post_id, specialty_id)), `user_interactions` (id, user_id, target_type VARCHAR(20), target_id BIGINT, type VARCHAR(20), created_at, unique(user_id, target_type, target_id, type)). Add indexes on posts(user_id), posts(created_at), comments(post_id), user_interactions(target_type, target_id).

- [ ] **Step 2: Verify migration runs**

Run: `cd backend && ./mvnw test -Dtest=ApplicationTests -pl . 2>&1 | tail -20`
Expected: Application context loads, Flyway applies V3 successfully.

---

### Task 2: Seed Data Migration (V4)

**Files:**
- Create: `backend/src/main/resources/db/migration/V4__community_seed_data.sql`

- [ ] **Step 1: Create V4 seed SQL**

Insert 6 mock users with Chinese nicknames (e.g., 张医生, 李患者, 王术后恢复, 赵看病日记, 孙陪诊员, 刘康复中). Insert 4-5 sample posts with varying like/comment/favorite counts, some with hospital/specialty tags. Insert 8-10 sample comments (mix of top-level and replies). Insert sample interactions.

- [ ] **Step 2: Verify seed data loads**

Run: `cd backend && ./mvnw test -Dtest=ApplicationTests 2>&1 | tail -10`
Expected: PASS, all migrations applied.

---

### Task 3: MockUser Entity + Repository + Controller + Test

**Files:**
- Create: `backend/src/main/java/com/firstprojct/model/MockUser.java`
- Create: `backend/src/main/java/com/firstprojct/repository/MockUserRepository.java`
- Create: `backend/src/main/java/com/firstprojct/dto/MockUserDto.java`
- Create: `backend/src/main/java/com/firstprojct/controller/MockUserController.java`
- Create: `backend/src/test/java/com/firstprojct/controller/MockUserControllerTest.java`

**Spec reference (mock-users/spec.md):**
- WHEN 系统启动 THEN mock_users 表有 5-8 个用户
- WHEN 请求 Mock 用户列表 THEN 返回所有用户的 id, nickname, avatar_url

- [ ] **Step 1: Write failing test** — `MockUserControllerTest`: GET /api/mock-users returns 200 with array of mock users, each having id/nickname/avatarUrl.
- [ ] **Step 2: Run test, verify RED** — Expected: 404 (endpoint doesn't exist yet).
- [ ] **Step 3: Create MockUser entity** — Map to `mock_users` table with id, nickname, avatarUrl fields.
- [ ] **Step 4: Create MockUserRepository** — `JpaRepository<MockUser, Long>`.
- [ ] **Step 5: Create MockUserDto** — `record MockUserDto(Long id, String nickname, String avatarUrl)`.
- [ ] **Step 6: Create MockUserController** — `GET /api/mock-users` returns `List<MockUserDto>` from repository.
- [ ] **Step 7: Run test, verify GREEN**.
- [ ] **Step 8: Commit**.

---

### Task 4: Post Entity + Repository

**Files:**
- Create: `backend/src/main/java/com/firstprojct/model/Post.java`
- Create: `backend/src/main/java/com/firstprojct/model/PostHospitalTag.java`
- Create: `backend/src/main/java/com/firstprojct/model/PostSpecialtyTag.java`
- Create: `backend/src/main/java/com/firstprojct/repository/PostRepository.java`

- [ ] **Step 1: Create Post entity** — Map to `posts` table. Fields: id, userId (FK), title, content, likeCount, favoriteCount, commentCount, createdAt, updatedAt. Add `@OneToMany` for PostHospitalTag and PostSpecialtyTag collections. Add `@ManyToOne` for MockUser.
- [ ] **Step 2: Create PostHospitalTag entity** — Map to `post_hospital_tags`. Fields: id, post (ManyToOne), hospital (ManyToOne).
- [ ] **Step 3: Create PostSpecialtyTag entity** — Map to `post_specialty_tags`. Fields: id, post (ManyToOne), specialty (ManyToOne).
- [ ] **Step 4: Create PostRepository** — `JpaRepository<Post, Long>` with custom queries: `findAllByOrderByCreatedAtDesc(Pageable)`, `findAllByOrderByLikeCountDescCommentCountDesc(Pageable)` (hot sort), `findByHospitalTagsHospitalId(Long hospitalId, Pageable)`, `findBySpecialtyTagsSpecialtyId(Long specialtyId, Pageable)`.
- [ ] **Step 5: Verify compiles** — `cd backend && ./mvnw compile`.

---

### Task 5: Post DTOs + Service + Controller + Tests

**Files:**
- Create: `backend/src/main/java/com/firstprojct/dto/PostDto.java`
- Create: `backend/src/main/java/com/firstprojct/dto/PostDetailDto.java`
- Create: `backend/src/main/java/com/firstprojct/dto/CreatePostRequest.java`
- Create: `backend/src/main/java/com/firstprojct/service/PostService.java`
- Create: `backend/src/main/java/com/firstprojct/controller/PostController.java`
- Create: `backend/src/test/java/com/firstprojct/controller/PostControllerTest.java`

**Spec reference (community-posts/spec.md):**
- WHEN 创建帖子含标题和内容 THEN 成功返回详情
- WHEN 标题或内容为空 THEN 返回验证错误
- WHEN 按最新排序 THEN 按创建时间倒序
- WHEN 按最热排序 THEN 按 (like_count + comment_count) 倒序
- WHEN 帖子存在 THEN 返回完整详情（含关联医院/专科）
- WHEN 帖子不存在 THEN 404
- WHEN 按医院/专科查询 THEN 返回关联帖子列表

- [ ] **Step 1: Write failing tests** — PostControllerTest with 7 test methods covering all spec scenarios: createPost_success, createPost_emptyTitle_returns400, listPosts_sortByLatest, listPosts_sortByHot, getPost_detail, getPost_notFound_returns404, getPostsByHospital, getPostsBySpecialty.
- [ ] **Step 2: Run tests, verify RED**.
- [ ] **Step 3: Create PostDto record** — id, title, contentPreview (first 200 chars), authorNickname, authorAvatarUrl, likeCount, commentCount, createdAt.
- [ ] **Step 4: Create PostDetailDto record** — Full content, plus hospitals list, specialties list, favoriteCount.
- [ ] **Step 5: Create CreatePostRequest record** — title (NotBlank), content (NotBlank), userId, hospitalIds (List<Long>), specialtyIds (List<Long>).
- [ ] **Step 6: Create PostService** — createPost(), listPosts(sort, pageable), getPostDetail(id), getPostsByHospital(hospitalId, pageable), getPostsBySpecialty(specialtyId, pageable).
- [ ] **Step 7: Create PostController** — Wire all endpoints per design.md API table.
- [ ] **Step 8: Run tests, verify GREEN**.
- [ ] **Step 9: Commit**.

---

### Task 6: Comment Entity + Repository + DTOs + Service + Controller + Tests

**Files:**
- Create: `backend/src/main/java/com/firstprojct/model/Comment.java`
- Create: `backend/src/main/java/com/firstprojct/repository/CommentRepository.java`
- Create: `backend/src/main/java/com/firstprojct/dto/CommentDto.java`
- Create: `backend/src/main/java/com/firstprojct/dto/CreateCommentRequest.java`
- Create: `backend/src/main/java/com/firstprojct/service/CommentService.java`
- Create: `backend/src/main/java/com/firstprojct/controller/CommentController.java`
- Create: `backend/src/test/java/com/firstprojct/controller/CommentControllerTest.java`

**Spec reference (community-comments/spec.md):**
- WHEN 发表顶级评论 THEN 创建成功, comment_count+1
- WHEN 帖子不存在 THEN 404
- WHEN 评论内容为空 THEN 验证错误
- WHEN 回复顶级评论 THEN 创建成功, parent_id 指向顶级
- WHEN 回复一条回复 THEN 拒绝, 提示只允许一层嵌套
- WHEN 获取评论列表 THEN 顶级评论正序 + 嵌套回复正序

- [ ] **Step 1: Write failing tests** — CommentControllerTest with 6 test methods: createTopLevelComment, createComment_postNotFound, createComment_emptyContent, replyToTopLevelComment, replyToReply_rejected, getComments_withNesting.
- [ ] **Step 2: Run tests, verify RED**.
- [ ] **Step 3: Create Comment entity** — id, postId (FK), userId (FK→mock_users), parentId (FK→comments, nullable), content, likeCount, createdAt. Add ManyToOne for post, user, parent.
- [ ] **Step 4: Create CommentRepository** — findByPostIdAndParentIdIsNullOrderByCreatedAtAsc, findByParentIdOrderByCreatedAtAsc.
- [ ] **Step 5: Create CommentDto record** — id, content, authorNickname, authorAvatarUrl, likeCount, createdAt, replies (List<CommentDto>).
- [ ] **Step 6: Create CreateCommentRequest record** — content (NotBlank), userId, parentId (nullable).
- [ ] **Step 7: Create CommentService** — createComment(postId, request) with nesting validation, getComments(postId) assembling tree.
- [ ] **Step 8: Create CommentController** — GET/POST /api/posts/{postId}/comments.
- [ ] **Step 9: Run tests, verify GREEN**.
- [ ] **Step 10: Commit**.

---

### Task 7: Interaction Entity + Service + Controller + Tests

**Files:**
- Create: `backend/src/main/java/com/firstprojct/model/UserInteraction.java`
- Create: `backend/src/main/java/com/firstprojct/repository/UserInteractionRepository.java`
- Create: `backend/src/main/java/com/firstprojct/service/InteractionService.java`
- Create: `backend/src/main/java/com/firstprojct/controller/InteractionController.java`
- Create: `backend/src/test/java/com/firstprojct/controller/InteractionControllerTest.java`

**Spec reference (community-interactions/spec.md):**
- WHEN 点赞未赞帖子 THEN like_count+1, 返回已点赞
- WHEN 取消点赞 THEN like_count-1, 返回未点赞
- WHEN 收藏/取消收藏 THEN favorite_count±1
- WHEN 点赞/取消评论 THEN comment.like_count±1

- [ ] **Step 1: Write failing tests** — InteractionControllerTest: likePost_toggle, unlikePost_toggle, favoritePost_toggle, unfavoritePost_toggle, likeComment_toggle, unlikeComment_toggle.
- [ ] **Step 2: Run tests, verify RED**.
- [ ] **Step 3: Create UserInteraction entity** — id, userId, targetType (POST/COMMENT), targetId, type (LIKE/FAVORITE), createdAt.
- [ ] **Step 4: Create UserInteractionRepository** — findByUserIdAndTargetTypeAndTargetIdAndType, deleteByUserIdAndTargetTypeAndTargetIdAndType.
- [ ] **Step 5: Create InteractionService** — toggleLikePost, toggleFavoritePost, toggleLikeComment (find existing → delete + decrement, or create + increment).
- [ ] **Step 6: Create InteractionController** — POST /api/posts/{id}/like, POST /api/posts/{id}/favorite, POST /api/comments/{id}/like. All accept userId as request param.
- [ ] **Step 7: Run tests, verify GREEN**.
- [ ] **Step 8: Commit**.

---

### Task 8: Frontend Types + API Service

**Files:**
- Modify: `frontend/src/types/index.ts`
- Modify: `frontend/src/services/api.ts`

- [ ] **Step 1: Add TypeScript types** — MockUser, Post (list summary), PostDetail (full detail with hospitals/specialties), Comment (with nested replies), CreatePostRequest, CreateCommentRequest, InteractionResponse.
- [ ] **Step 2: Add API functions** — fetchMockUsers, fetchPosts(sort, page), fetchPostDetail(id), fetchPostsByHospital(hospitalId), fetchPostsBySpecialty(specialtyId), createPost(data), fetchComments(postId), createComment(postId, data), toggleLikePost(postId, userId), toggleFavoritePost(postId, userId), toggleLikeComment(commentId, userId).
- [ ] **Step 3: Verify compiles** — `cd frontend && npx tsc --noEmit`.

---

### Task 9: Community Pages + Routing + Navigation

**Files:**
- Create: `frontend/src/pages/CommunityPage.tsx`
- Create: `frontend/src/pages/PostDetailPage.tsx`
- Create: `frontend/src/pages/NewPostPage.tsx`
- Modify: `frontend/src/App.tsx`
- Modify: `frontend/src/components/Layout.tsx`

- [ ] **Step 1: Create CommunityPage** — Post list with latest/hot toggle tabs, pagination, post cards showing title/preview/author/likes/comments/time. Link to post detail. "发帖" button linking to /community/new.
- [ ] **Step 2: Create PostDetailPage** — Full post content, author info, hospital/specialty tags as chips, like/favorite buttons with counts, comment section with top-level + nested replies, comment input form.
- [ ] **Step 3: Create NewPostPage** — Form with title, content textarea, mock user dropdown, optional hospital multi-select, optional specialty multi-select. Submit calls createPost API.
- [ ] **Step 4: Add routes in App.tsx** — /community → CommunityPage, /community/posts/:id → PostDetailPage, /community/new → NewPostPage.
- [ ] **Step 5: Add "交流" nav link in Layout.tsx** — Add navigation link next to search bar.
- [ ] **Step 6: Verify frontend builds** — `cd frontend && npm run build`.

---

### Task 10: Related Posts on Hospital/Specialty Pages

**Files:**
- Modify: `frontend/src/pages/HospitalDetailPage.tsx`
- Modify: `frontend/src/pages/SpecialtyRankingPage.tsx`

- [ ] **Step 1: Add related posts to HospitalDetailPage** — Fetch posts by hospital ID, display 3-5 most recent as a "患者经验" section below hospital details.
- [ ] **Step 2: Add related posts to SpecialtyRankingPage** — Fetch posts by specialty ID, display related posts section below rankings.
- [ ] **Step 3: Verify builds and manual check** — `cd frontend && npm run build`.
