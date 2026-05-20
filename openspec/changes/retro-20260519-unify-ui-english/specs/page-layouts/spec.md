## MODIFIED Requirements

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

## ADDED Requirements

### Requirement: All UI chrome text in English
All developer-authored text in the frontend application (navigation labels, button text, placeholders, headings, empty state messages, error messages) SHALL be in English. Chinese text from the database (hospital nameCn, specialty nameCn, addresses) is preserved as informational content.

#### Scenario: No Chinese UI labels in source
- **WHEN** a developer runs `grep` for Chinese characters in `frontend/src/`
- **THEN** no matches SHALL exist for developer-authored UI labels (only data references like `{hospital.nameCn}` are acceptable)

#### Scenario: HTML lang attribute
- **WHEN** the HTML document loads
- **THEN** the `<html>` element SHALL have `lang="en"` attribute
