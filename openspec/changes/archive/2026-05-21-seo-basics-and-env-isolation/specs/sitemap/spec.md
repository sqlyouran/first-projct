## ADDED Requirements

### Requirement: XML sitemap endpoint
The backend SHALL expose a public `GET /sitemap.xml` endpoint that returns a valid XML sitemap.

#### Scenario: Sitemap accessible without authentication
- **WHEN** any client (including search engine crawlers) sends GET /sitemap.xml
- **THEN** the server SHALL respond with HTTP 200 and Content-Type: application/xml

#### Scenario: Sitemap includes all hospital pages
- **WHEN** the sitemap is generated
- **THEN** it SHALL include one `<url>` entry per hospital with loc: `{site-url}/hospitals/{id}`

#### Scenario: Sitemap includes all specialty ranking pages
- **WHEN** the sitemap is generated
- **THEN** it SHALL include one `<url>` entry per specialty with loc: `{site-url}/specialties/{id}`

#### Scenario: Sitemap format is valid XML
- **WHEN** the sitemap XML is parsed
- **THEN** it SHALL conform to the sitemaps.org 0.9 schema with `<urlset>` root element

### Requirement: robots.txt file
The frontend SHALL include a `robots.txt` file that allows all crawlers and points to the sitemap.

#### Scenario: robots.txt present
- **WHEN** a crawler requests /robots.txt
- **THEN** the server SHALL return a file with `User-agent: *`, `Allow: /`, and a `Sitemap:` directive

#### Scenario: Sitemap directive points to correct URL
- **WHEN** robots.txt is served
- **THEN** the `Sitemap:` line SHALL reference the backend sitemap endpoint
