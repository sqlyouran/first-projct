## ADDED Requirements

### Requirement: Hospital inquiry submission
Authenticated users SHALL be able to submit an inquiry to a specific hospital from the hospital detail page.

#### Scenario: Submitting an inquiry
- **WHEN** an authenticated user fills in the inquiry form (name, email, condition summary, preferred date) and submits
- **THEN** the system SHALL create an Inquiry record with status PENDING and return success confirmation

#### Scenario: Inquiry requires authentication
- **WHEN** an unauthenticated user clicks "Send Inquiry"
- **THEN** the user SHALL be redirected to the login page

#### Scenario: Inquiry form validation
- **WHEN** a user submits an inquiry with empty name or email
- **THEN** the system SHALL reject the submission with a validation error

### Requirement: Inquiry CTA on hospital detail page
The hospital detail page SHALL display a prominent "Send Inquiry" call-to-action button.

#### Scenario: CTA button visible
- **WHEN** a user views a hospital detail page
- **THEN** a "Send Inquiry" button SHALL be prominently displayed in the action area

#### Scenario: CTA opens modal form
- **WHEN** a user clicks "Send Inquiry"
- **THEN** a modal dialog SHALL appear with the inquiry form, pre-filled with the hospital name

#### Scenario: Successful submission feedback
- **WHEN** the inquiry is successfully submitted
- **THEN** the modal SHALL show a success message with estimated response time ("We typically respond within 2 business days")

### Requirement: Inquiry button on specialty ranking page
Each hospital row in the specialty ranking table SHALL have an "Inquire" action button.

#### Scenario: Inquire button in ranking row
- **WHEN** a user views the specialty ranking page
- **THEN** each hospital row SHALL display a small "Inquire" button

#### Scenario: Inquire button navigation
- **WHEN** a user clicks "Inquire" on a ranking row
- **THEN** the user SHALL be navigated to that hospital's detail page with the inquiry modal automatically opened

### Requirement: My Inquiries page
Authenticated users SHALL be able to view their submitted inquiries in their profile area.

#### Scenario: Viewing my inquiries
- **WHEN** an authenticated user navigates to /my-inquiries
- **THEN** the system SHALL display all inquiries submitted by that user, sorted by newest first

#### Scenario: Inquiry status display
- **WHEN** an inquiry is displayed in the list
- **THEN** it SHALL show the hospital name, condition summary, submission date, and current status (Pending / Contacted / Closed)

#### Scenario: No inquiries yet
- **WHEN** a user with no inquiries visits /my-inquiries
- **THEN** the page SHALL display an empty state message encouraging the user to browse hospitals

### Requirement: Inquiry data model
The system SHALL store inquiries with fields: id, userId, hospitalId, name, email, conditionSummary, preferredDate, status, createdAt.

#### Scenario: Inquiry persisted correctly
- **WHEN** an inquiry is submitted
- **THEN** all fields SHALL be stored in the inquiries table with status defaulting to PENDING

#### Scenario: Inquiry linked to user and hospital
- **WHEN** an inquiry is created
- **THEN** it SHALL reference both the authenticated user (userId) and the target hospital (hospitalId) via foreign keys
