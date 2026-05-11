# Tasks: Medical Directory MVP

## Prerequisites
- [x] Proposal approved
- [x] Design reviewed

## Implementation Tasks

### Phase 1: Backend — Data Model & Seed Data
- [ ] T1. Define JPA entities: Specialty, Hospital, SpecialtyRanking
- [ ] T2. Create Flyway migration scripts (schema creation)
- [ ] T3. Create seed data (data.sql) — 10+ specialties, 50+ hospitals, rankings based on Fudan 2023
- [ ] T4. Write repository interfaces (JPA repositories)

### Phase 2: Backend — REST API
- [ ] T5. Implement GET /api/specialties (list all specialties)
- [ ] T6. Implement GET /api/specialties/{id}/rankings (with city/year filter)
- [ ] T7. Implement GET /api/hospitals (search with pagination, city/specialty filter)
- [ ] T8. Implement GET /api/hospitals/{id} (hospital detail with top specialties)
- [ ] T9. Write unit/integration tests for all API endpoints

### Phase 3: Frontend — Pages & Routing
- [ ] T10. Set up React Router + project structure + Tailwind CSS
- [ ] T11. Build Home page — specialty category grid + search bar
- [ ] T12. Build Specialty Ranking page — ranked hospital list with city filter
- [ ] T13. Build Hospital Profile page — detailed info + top specialties
- [ ] T14. Build Search Results page — hospital list with filters + pagination

### Phase 4: Integration & Polish
- [ ] T15. Connect frontend to backend APIs (replace any hardcoded data)
- [ ] T16. Add loading states, error handling, empty states
- [ ] T17. Responsive design verification (mobile/tablet/desktop)
- [ ] T18. End-to-end smoke test (full flow: home → specialty → ranking → hospital detail)

## Definition of Done
- [ ] All backend tests passing
- [ ] All frontend pages rendering correctly with API data
- [ ] Responsive on mobile devices
- [ ] Full user flow works: browse specialties → view rankings → view hospital detail
- [ ] Code review completed (code-review skill)
