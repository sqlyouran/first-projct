## ADDED Requirements

### Requirement: Sticky header with full navigation
The application SHALL have a sticky top header that remains visible during scroll, containing: logo/brand, navigation links (Home, Community), and a search input. All navigation labels SHALL be in English.

#### Scenario: Header stays visible on scroll
- **WHEN** a user scrolls down on any page
- **THEN** the header SHALL remain fixed at the top with a white/blur backdrop

#### Scenario: Navigation links present in English
- **WHEN** the header is rendered
- **THEN** it SHALL contain clickable links labeled "Home" (/) and "Community" (/community) in English

#### Scenario: Active link indication
- **WHEN** a user is on a specific section (e.g., community)
- **THEN** the corresponding nav link SHALL be visually distinguished (primary color, underline, or bold)

### Requirement: Homepage hero section
The homepage SHALL feature a prominent hero section above the specialty grid, with a large heading, descriptive subtitle, and a centered search input.

#### Scenario: Hero displays on homepage
- **WHEN** a user visits the homepage (/)
- **THEN** a hero section SHALL be visible with warm gradient background, large title text, subtitle, and a prominent search bar

#### Scenario: Hero search triggers navigation
- **WHEN** a user types a query in the hero search and submits
- **THEN** the system SHALL navigate to `/search?q=<query>` (same as header search)

#### Scenario: Hero not shown on other pages
- **WHEN** a user visits any page other than homepage
- **THEN** the hero section SHALL NOT be displayed

### Requirement: Rich footer with multiple columns
The application SHALL have a footer section with multiple columns: brand/description, useful links, and copyright.

#### Scenario: Footer structure
- **WHEN** any page renders to completion
- **THEN** a footer SHALL be displayed with at least: brand name + description, navigation links column, and copyright text

#### Scenario: Footer visual separation
- **WHEN** the footer is rendered
- **THEN** it SHALL be visually separated from content by a subtle top border or background color change

### Requirement: Content area uses max-w-7xl
All page content SHALL be constrained to `max-w-7xl` (80rem) width, wider than the current `max-w-6xl`, with consistent horizontal padding.

#### Scenario: Content width on wide screens
- **WHEN** a page is viewed on a screen wider than 80rem
- **THEN** content SHALL be centered with a maximum width of 80rem

### Requirement: Page-specific layout structures
Each page type SHALL follow its designated layout structure for consistent information hierarchy.

#### Scenario: Specialty ranking page layout
- **WHEN** a user views a specialty ranking page
- **THEN** it SHALL display: breadcrumb/back link → page title with specialty info → city filter → ranking table → related posts section, with clear visual separation between sections

#### Scenario: Hospital detail page layout
- **WHEN** a user views a hospital detail page
- **THEN** it SHALL display information in clearly labeled sections: header (name + international badge), info grid (location + contact), about, top specialties, and related posts

#### Scenario: Community page layout
- **WHEN** a user views the community page
- **THEN** it SHALL display: page header with "New Post" CTA button → sort tabs → post card list → pagination, with generous spacing

#### Scenario: Post detail page layout
- **WHEN** a user views a post detail page
- **THEN** it SHALL display: back navigation → post content card (title, meta, tags, body, actions) → comments section, as distinct visual blocks

### Requirement: All UI chrome text in English
All developer-authored text in the frontend application (navigation labels, button text, placeholders, headings, empty state messages, error messages) SHALL be in English. Chinese text from the database (hospital nameCn, specialty nameCn, addresses) is preserved as informational content.

#### Scenario: No Chinese UI labels in source
- **WHEN** a developer runs `grep` for Chinese characters in `frontend/src/`
- **THEN** no matches SHALL exist for developer-authored UI labels (only data references like `{hospital.nameCn}` are acceptable)

#### Scenario: HTML lang attribute
- **WHEN** the HTML document loads
- **THEN** the `<html>` element SHALL have `lang="en"` attribute
