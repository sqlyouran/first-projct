## ADDED Requirements

### Requirement: Color token system
The system SHALL define a set of CSS custom properties for all colors used throughout the application, using Tailwind v4's `@theme` directive in `index.css`.

#### Scenario: Primary color applied
- **WHEN** a user views any primary action element (buttons, links, active states)
- **THEN** the element SHALL use teal-700 (`#0f766e`) as the primary color

#### Scenario: Accent color for emphasis
- **WHEN** a call-to-action or highlighted badge is displayed
- **THEN** the element SHALL use amber-600 (`#d97706`) as the accent color

#### Scenario: Warm background
- **WHEN** a user views any page
- **THEN** the page background SHALL be warm off-white (`#faf9f7`) instead of cold gray-50

### Requirement: Typography system uses Inter font
The system SHALL load the Inter font from Google Fonts CDN and apply it as the primary font-family for all text elements.

#### Scenario: Font loads correctly
- **WHEN** any page renders
- **THEN** all text SHALL render in Inter font with `font-display: swap` to avoid blocking

#### Scenario: Font preconnect for performance
- **WHEN** the HTML document loads
- **THEN** `<link rel="preconnect" href="https://fonts.googleapis.com">` and `<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>` SHALL be present in `<head>`

### Requirement: Icon system uses Lucide React
The system SHALL use `lucide-react` package for all UI-chrome icons, replacing emoji usage for interaction indicators.

#### Scenario: Like icon replaces emoji
- **WHEN** a like button or like count is displayed
- **THEN** it SHALL use a Lucide `ThumbsUp` icon instead of 👍 emoji

#### Scenario: Comment icon replaces emoji
- **WHEN** a comment count is displayed
- **THEN** it SHALL use a Lucide `MessageCircle` icon instead of 💬 emoji

#### Scenario: Favorite icon replaces emoji
- **WHEN** a favorite/bookmark indicator is displayed
- **THEN** it SHALL use a Lucide `Star` or `Bookmark` icon instead of ⭐ emoji

#### Scenario: Specialty icons remain as emoji
- **WHEN** a medical specialty icon is displayed (data from backend)
- **THEN** it SHALL continue using the emoji stored in the database (not replaced by Lucide)

### Requirement: Card component pattern
All card-style containers SHALL use a consistent visual pattern: `rounded-2xl` border-radius, subtle shadow (no visible border), white background, and hover elevation transition.

#### Scenario: Card at rest
- **WHEN** a card component is displayed without interaction
- **THEN** it SHALL have white background, `border-radius: 1rem`, and a subtle shadow

#### Scenario: Card on hover
- **WHEN** a user hovers over a clickable card
- **THEN** the card SHALL smoothly transition to a deeper shadow with slight upward translate (`-translate-y-0.5`)

### Requirement: Spacing follows generous rhythm
All page sections SHALL use increased spacing compared to the current tight layout — minimum `gap-6` between card groups, `py-12` between major sections.

#### Scenario: Section spacing
- **WHEN** two distinct content sections are rendered on a page
- **THEN** they SHALL be separated by at least 48px (py-12) of vertical space
