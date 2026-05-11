# Proposal: Medical Directory MVP - China Healthcare Guide for Foreigners

## Problem Statement

Foreigners coming to China for medical treatment face significant barriers:
- Language barrier makes it difficult to find English-friendly hospitals/doctors
- No centralized English-language resource for China's healthcare system
- No way to understand which hospitals are top-ranked for specific specialties
- Existing Chinese platforms (好大夫, 复旦排行榜) are entirely in Chinese

## Proposed Solution

Build an **English-language medical directory website** that helps foreigners discover and evaluate hospitals in China **by specialty ranking**. The MVP focuses on presenting hospital rankings organized by medical specialty (e.g., "Top hospitals for Orthopedics"), making it easy for foreigners to identify the best hospitals for their specific condition.

### Key Features (MVP)
1. **Specialty Rankings** — Browse hospitals ranked by medical specialty
   - e.g., "Top 10 Orthopedics Hospitals in China"
   - e.g., "Best Cardiology Hospitals in Beijing"
   - Rankings displayed with rank position, hospital name, city, score/tier
2. **Hospital Directory** — Browse/search hospitals with English descriptions
   - Hospital profiles: address, contact, key specialties, international department info
   - Filter by city and specialty strength
3. **Specialty Categories** — Clear categorization of medical departments
   - Orthopedics, Cardiology, Oncology, Neurology, Ophthalmology, etc.
   - Each category shows ranked hospital list
4. **Search & Filter** — Search hospitals by name, city, or specialty
   - Filter rankings by city/region
5. **Responsive Design** — Mobile-first, works well on phone/tablet/desktop

### Out of Scope (for MVP)
- Doctor directory / individual doctor profiles
- User registration/login
- Online booking/appointment
- Community forum/discussion
- Real-time chat/messaging
- Payment integration
- User reviews/ratings

## Data Strategy
- **Phase 1 (MVP)**: Mock/seed data — manually curated ranking data for key specialties
- **Phase 2**: Web scraping from authoritative sources (e.g., 复旦医院排行榜, 中国医院科技量值排行) to keep rankings updated

## Target Users
- Foreigners living in or visiting China who need medical care
- Medical tourism patients researching Chinese hospitals by specialty
- International companies seeking healthcare partners for expat employees

## Success Criteria
- [ ] User can browse a list of medical specialties
- [ ] User can view top-ranked hospitals for a selected specialty
- [ ] User can search hospitals by name, city, or specialty
- [ ] User can filter specialty rankings by city/region
- [ ] User can view detailed hospital profile page (English)
- [ ] Website is fully in English
- [ ] Responsive design works on mobile devices
- [ ] Page loads under 2 seconds
- [ ] Mock data covers at least 10 specialties with 10+ hospitals each

## Risks & Open Questions
- Ranking methodology: follow existing Chinese rankings (复旦版) or create own scoring?
- How frequently should rankings be refreshed once scraping is in place?
- Do we need a simple admin panel for data management in MVP, or is DB seeding sufficient?
