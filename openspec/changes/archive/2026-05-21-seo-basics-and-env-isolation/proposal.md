## Why

The site is a React SPA (client-side rendering), which means search engines see an empty `<div id="root">` on first crawl — making hospital and specialty pages effectively invisible to organic search. Additionally, sensitive configuration (JWT secret, CORS origins) is hardcoded in `application.yaml` and committed to the repository, creating a security risk before any production deployment. Both issues must be addressed before the site can be meaningfully discovered or safely operated.

## What Changes

- Install `react-helmet-async` and add dynamic `<title>`, `<meta name="description">`, and `og:*` tags to all major pages (HomePage, HospitalDetailPage, SpecialtyRankingPage, PostDetailPage, CommunityPage)
- Add JSON-LD structured data (Hospital schema) to HospitalDetailPage
- Add JSON-LD structured data (MedicalSpecialty schema) to SpecialtyRankingPage
- Add `GET /sitemap.xml` backend endpoint that dynamically generates a sitemap from all hospital and specialty IDs
- Add `public/robots.txt` to the frontend pointing crawlers to the sitemap
- Move hardcoded `jwt.secret` to environment variable `${JWT_SECRET}` with a dev fallback
- Move hardcoded CORS `allowedOrigins` (`localhost:5173`) to configurable property `${app.cors.allowed-origins}` in both `SecurityConfig` and `WebConfig`
- Add `application-prod.yaml` as a production environment template referencing all required env vars

## Capabilities

### New Capabilities
- `seo-meta-tags`: Dynamic page titles, meta descriptions, and Open Graph tags across all major pages
- `sitemap`: Backend-generated XML sitemap covering all hospital and specialty ranking pages
- `env-config-isolation`: Environment-specific configuration with secrets externalized to environment variables

### Modified Capabilities
- `page-layouts`: CommunityPage and PostDetailPage gain meta tag support (minor addition, no behavioral change)

## Impact

- **Frontend**: Add `react-helmet-async` dependency; wrap `<App>` in `<HelmetProvider>`; update 5 pages
- **Backend**: New `SitemapController`; new `/sitemap.xml` public endpoint (added to Spring Security permitAll); `SecurityConfig` and `WebConfig` read CORS from `@Value`; new `application-prod.yaml`
- **Configuration**: `application.yaml` modified; `application-prod.yaml` added; `.env.example` recommended
- **No breaking changes** to existing API contracts or user-facing behavior
