## ADDED Requirements

### Requirement: Post type classification
The system SHALL support two post types: DISCUSSION (default, backward-compatible) and STORY (structured patient journey).

#### Scenario: Creating a discussion post
- **WHEN** a user creates a post without specifying type
- **THEN** the post type SHALL default to DISCUSSION

#### Scenario: Creating a patient story post
- **WHEN** a user creates a post with type STORY
- **THEN** the system SHALL accept additional structured fields: condition, treatmentType, costRange, timelineDays, outcome, nationality

#### Scenario: Filtering posts by type
- **WHEN** a client requests GET /api/posts?type=STORY
- **THEN** only posts with type STORY SHALL be returned

### Requirement: Patient Story structured fields
A STORY post SHALL include optional structured fields for medical journey data.

#### Scenario: Story with all structured fields
- **WHEN** a user fills in all structured fields (condition, treatmentType, costRange, timelineDays, outcome, nationality)
- **THEN** all fields SHALL be stored and returned in the post detail response

#### Scenario: Story with partial structured fields
- **WHEN** a user fills in only condition and outcome (other fields left blank)
- **THEN** the story SHALL be saved successfully with null values for unfilled structured fields

#### Scenario: Cost range uses predefined intervals
- **WHEN** a user selects a cost range
- **THEN** the available options SHALL be: UNDER_5K, 5K_10K, 10K_25K, 25K_50K, OVER_50K (in USD)

#### Scenario: Outcome uses predefined ratings
- **WHEN** a user selects a treatment outcome
- **THEN** the available options SHALL be: EXCELLENT, GOOD, FAIR, POOR

### Requirement: Stories tab in community page
The community page SHALL include a Stories tab alongside Latest and Hot tabs.

#### Scenario: User clicks Stories tab
- **WHEN** a user clicks the "Stories" tab on the community page
- **THEN** only posts with type STORY SHALL be displayed, sorted by latest

### Requirement: Featured Stories on homepage
The homepage SHALL display up to 3 featured patient stories below the hero section.

#### Scenario: Featured stories loaded
- **WHEN** the homepage renders
- **THEN** up to 3 STORY posts SHALL be displayed, sorted by engagement (likes + comments)

#### Scenario: No stories exist
- **WHEN** there are no STORY posts in the system
- **THEN** the Featured Stories section SHALL not be rendered

### Requirement: Story card highlights key metrics
Story cards SHALL prominently display cost range, outcome, and timeline alongside the title.

#### Scenario: Story card rendering
- **WHEN** a story card is displayed in a list
- **THEN** it SHALL show badge-style indicators for cost range (e.g. "$5K-10K"), outcome (e.g. "Excellent"), and timeline days (e.g. "14 days")

#### Scenario: Missing metrics gracefully handled
- **WHEN** a story has no cost range or timeline filled
- **THEN** the corresponding badge SHALL not be shown (no "N/A" placeholder)

### Requirement: New Story creation form
The system SHALL provide a dedicated form for creating Patient Story posts with structured field inputs.

#### Scenario: Navigating to new story form
- **WHEN** an authenticated user clicks "Share Your Story" on the community page
- **THEN** the user SHALL be navigated to a form with structured inputs (condition, treatment type, cost range dropdown, timeline days, outcome dropdown, nationality) plus the standard title and content fields

#### Scenario: Story form requires authentication
- **WHEN** an unauthenticated user attempts to access the story form
- **THEN** the user SHALL be redirected to the login page

### Requirement: English seed data for patient stories
The V7 migration SHALL include English-language patient story seed data to align with the product's international user positioning.

#### Scenario: Seed stories populated
- **WHEN** the application starts with a fresh database
- **THEN** at least 5 English-language patient stories SHALL be present in the community, covering diverse specialties (orthopedics, cardiology, oncology, etc.)

#### Scenario: Existing Chinese seed data preserved
- **WHEN** V7 migration runs after V4
- **THEN** existing Chinese seed posts SHALL remain intact alongside new English stories
