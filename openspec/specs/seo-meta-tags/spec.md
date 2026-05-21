# SEO Meta Tags

## Purpose

Define the SEO metadata each major page must render so search engines and social platforms can index and preview content correctly. The frontend uses `react-helmet-async` to inject these tags per route at runtime.

## Requirements

### Requirement: Dynamic page title per route
Each major page SHALL render a unique, descriptive `<title>` tag reflecting the page content.

#### Scenario: HomePage title
- **WHEN** a user or crawler loads the homepage
- **THEN** the document title SHALL be "ChinaMedGuide — Find English-Friendly Hospitals in China"

#### Scenario: HospitalDetailPage title
- **WHEN** a hospital detail page loads
- **THEN** the document title SHALL be "{hospital.name} | ChinaMedGuide"

#### Scenario: SpecialtyRankingPage title
- **WHEN** a specialty ranking page loads
- **THEN** the document title SHALL be "Top {specialty.name} Hospitals in China | ChinaMedGuide"

#### Scenario: PostDetailPage title
- **WHEN** a post detail page loads
- **THEN** the document title SHALL be "{post.title} | ChinaMedGuide Community"

#### Scenario: CommunityPage title
- **WHEN** the community list page loads
- **THEN** the document title SHALL be "Community — Patient Stories & Discussions | ChinaMedGuide"

### Requirement: Meta description on all major pages
Each major page SHALL include a `<meta name="description">` tag with a concise, keyword-rich description.

#### Scenario: Description present
- **WHEN** any of the five major pages renders
- **THEN** a `<meta name="description">` tag SHALL be present with content between 120–160 characters

#### Scenario: HospitalDetailPage description includes hospital name
- **WHEN** a hospital detail page renders
- **THEN** the description SHALL include the hospital name and city

### Requirement: Open Graph tags on major pages
Each major page SHALL include `og:title`, `og:description`, and `og:type` tags for social sharing previews.

#### Scenario: OG tags present
- **WHEN** any of the five major pages renders
- **THEN** `og:title`, `og:description`, and `og:type` SHALL all be present

#### Scenario: Hospital page OG type
- **WHEN** a hospital detail page renders
- **THEN** `og:type` SHALL be "website"

### Requirement: JSON-LD Hospital structured data
HospitalDetailPage SHALL include a `<script type="application/ld+json">` block with Schema.org Hospital markup.

#### Scenario: Hospital schema present
- **WHEN** a hospital detail page loads
- **THEN** a JSON-LD block SHALL be present with `@type: "Hospital"`, `name`, `address`, and `telephone` fields

### Requirement: JSON-LD MedicalSpecialty structured data
SpecialtyRankingPage SHALL include a `<script type="application/ld+json">` block with Schema.org MedicalSpecialty markup.

#### Scenario: MedicalSpecialty schema present
- **WHEN** a specialty ranking page loads
- **THEN** a JSON-LD block SHALL be present with `@type: "MedicalSpecialty"` and `name` field
