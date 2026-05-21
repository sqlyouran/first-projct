## 1. Frontend Setup

- [x] 1.1 Install `react-helmet-async` dependency (`npm install react-helmet-async`)
- [x] 1.2 Wrap `<App>` with `<HelmetProvider>` in `frontend/src/main.tsx`
- [x] 1.3 Create `frontend/public/robots.txt` with `User-agent: *`, `Allow: /`, and `Sitemap: {backend-url}/sitemap.xml`

## 2. SEO Meta Tags — Static & List Pages

- [x] 2.1 Add `<Helmet>` to `HomePage.tsx` with title, meta description, and og:* tags
- [x] 2.2 Add `<Helmet>` to `CommunityPage.tsx` with title, meta description, and og:* tags

## 3. SEO Meta Tags — Dynamic Pages

- [x] 3.1 Add `<Helmet>` to `HospitalDetailPage.tsx` — title includes `hospital.name`, description includes name + city
- [x] 3.2 Add JSON-LD Hospital schema `<script type="application/ld+json">` to `HospitalDetailPage.tsx` with `@type: "Hospital"`, name, address, telephone
- [x] 3.3 Add `<Helmet>` to `SpecialtyRankingPage.tsx` — title includes `specialty.name`
- [x] 3.4 Add JSON-LD MedicalSpecialty schema `<script type="application/ld+json">` to `SpecialtyRankingPage.tsx`
- [x] 3.5 Add `<Helmet>` to `PostDetailPage.tsx` — title includes `post.title`

## 4. Backend Sitemap Endpoint

- [x] 4.1 Add `app.site-url` property to `application.yaml` (default: `http://localhost:5173`)
- [x] 4.2 Create `SitemapController.java` at `/sitemap.xml` — GET, produces `application/xml`
- [x] 4.3 `SitemapController` queries all hospital IDs from `HospitalRepository` and all specialty IDs from `SpecialtyRepository`
- [x] 4.4 Generate `<urlset>` XML with one `<url><loc>` per hospital and specialty
- [x] 4.5 Add `/sitemap.xml` to `SecurityConfig` permitAll rules

## 5. Environment Config Isolation — Backend

- [x] 5.1 Update `application.yaml`: change `jwt.secret` to `${JWT_SECRET:defaultDevSecretOnlyUsedLocallyNeverInProd256bit!!}`
- [x] 5.2 Update `application.yaml`: add `app.cors.allowed-origins: ${CORS_ORIGINS:http://localhost:5173}` property
- [x] 5.3 Update `SecurityConfig.java`: inject `@Value("${app.cors.allowed-origins}") List<String> corsOrigins` and use it in `corsConfigurationSource()`
- [x] 5.4 Update `WebConfig.java`: inject `@Value("${app.cors.allowed-origins}") List<String> corsOrigins` and use it in `addCorsMappings()`
- [x] 5.5 Create `application-prod.yaml` with all required production env var placeholders: `${DB_URL}`, `${DB_USERNAME}`, `${DB_PASSWORD}`, `${JWT_SECRET}`, `${CORS_ORIGINS}`, `${MAIL_HOST}`, `${MAIL_PORT}`, `${MAIL_USERNAME}`, `${MAIL_PASSWORD}`, h2-console disabled

## 6. Verification

- [x] 6.1 Run backend tests — all existing tests must pass (`./mvnw test`) — 35/35 PASSED
- [x] 6.2 Run frontend build — must compile without errors (`npm run build`) — built in 1.24s
- [x] 6.3 Start backend locally and verify `GET http://localhost:8080/sitemap.xml` returns valid XML with hospital and specialty URLs — verified, 25 hospitals + 12 specialties + home + community URLs returned
- [x] 6.4 Verify `robots.txt` is served correctly from frontend dev server at `http://localhost:5173/robots.txt` — file exists in `public/` and is bundled into `dist/`, Vite serves it from public root automatically
- [x] 6.5 Inspect Helmet output — confirmed all 5 pages (`HomePage`, `CommunityPage`, `HospitalDetailPage`, `SpecialtyRankingPage`, `PostDetailPage`) include `<Helmet>` blocks; Hospital + Specialty pages include JSON-LD scripts (`@type: "Hospital"`, `@type: "MedicalSpecialty"`)
- [x] 6.6 Confirm backend starts without `JWT_SECRET` env var set (uses fallback default) — backend started in 3.085s without `JWT_SECRET` set
