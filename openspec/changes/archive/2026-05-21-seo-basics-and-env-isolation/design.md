## Context

The site is a React 19 + Vite SPA backed by Spring Boot 3.4. All page content is rendered client-side, meaning search engine crawlers receive an empty HTML shell on initial request. No meta tags, structured data, sitemap, or robots.txt currently exist. Additionally, `application.yaml` contains a hardcoded JWT secret and CORS origin (`localhost:5173`) committed to the repository — both must be externalized before any production deployment.

## Goals / Non-Goals

**Goals:**
- Enable Google and other crawlers to correctly index the site's key pages with meaningful titles and descriptions
- Provide a machine-readable sitemap listing all hospital and specialty ranking pages
- Externalize all environment-specific secrets and config to environment variables
- Establish a `application-prod.yaml` template that documents all required production variables

**Non-Goals:**
- Server-side rendering (SSR) or static generation — this remains a CSR SPA
- Full SEO audit or content optimization
- Implementing a real production database or email provider (that is a separate deployment concern)
- Rate limiting or other security hardening

## Decisions

### D1: react-helmet-async over React 19 native document metadata

React 19 supports `<title>` and `<meta>` as first-class elements without a library. However, `react-helmet-async` provides more complete support including `og:*` tags and JSON-LD `<script>` injection in a single, well-tested API surface. React 19's native support is still limited to basic tags. Using `react-helmet-async` avoids fragmentation.

### D2: JSON-LD via `<script type="application/ld+json">` injected via Helmet

Alternative considered: Microdata HTML attributes inline in JSX. JSON-LD is the Google-recommended format, is easier to maintain separately from markup, and integrates cleanly with react-helmet-async.

### D3: Sitemap generated server-side (Spring Boot endpoint) rather than statically at build time

Hospital and specialty data lives in the database. A static sitemap built at deploy time would be stale immediately. A dynamic `/sitemap.xml` endpoint queries `HospitalRepository` and `SpecialtyRepository` at request time. Sitemap is read-only and public — no auth required.

Sitemap URL pattern:
```
https://{SITE_URL}/hospitals/{id}
https://{SITE_URL}/specialties/{id}
```

The `SITE_URL` will be configurable via `app.site-url` property (defaults to `http://localhost:5173` for dev).

### D4: CORS origins as `@Value`-injected List

Both `SecurityConfig.corsConfigurationSource()` and `WebConfig.addCorsMappings()` currently hardcode `http://localhost:5173`. Both will read from `${app.cors.allowed-origins}` as a comma-separated string (Spring supports `List<String>` injection). Default in `application.yaml` preserves current dev behavior.

### D5: JWT secret uses SpEL default expression

```yaml
jwt:
  secret: ${JWT_SECRET:defaultDevSecretOnlyUsedLocallyNeverInProd256bit!!}
```

The default value makes local dev work without any `.env` file. CI/prod must supply `JWT_SECRET` — the default string clearly communicates it is dev-only.

## Risks / Trade-offs

- **CSR indexing still imperfect** → Google does execute JS eventually, but timing is unpredictable. Meta tags help signal intent; full indexing of dynamic content requires SSR (future phase 2). Mitigation: submit sitemap to Google Search Console after deployment.
- **Sitemap on backend, `robots.txt` on frontend** → In a same-origin nginx deployment these both appear at the same domain. In a split-domain deployment (frontend CDN + backend API), `robots.txt` points to `api.example.com/sitemap.xml` which may be blocked. Mitigation: document this in `application-prod.yaml` comments.
- **application-prod.yaml committed to repo** → It contains only `${ENV_VAR}` placeholders, no actual secrets. This is intentional — it is a template, not a secrets file.

## Migration Plan

1. Backend changes compile and pass tests independently
2. Frontend changes: `npm install react-helmet-async`, wrap `<App>` in `<HelmetProvider>` in `main.tsx`, add Helmet to each page
3. `application.yaml` change is backward-compatible: local dev continues to work without setting any env vars (default fallback covers it)
4. `application-prod.yaml` is inert until `spring.profiles.active=prod` is set

## Open Questions

- Should `app.site-url` be a required property in prod (fail-fast if missing) or have a sensible fallback? → Default to empty string so sitemap still generates; operators should set it explicitly.
- Should community post pages (`/community/posts/:id`) be included in the sitemap? → Out of scope for this change; can be added later when content volume grows.
