## Context

The application targets international residents in China seeking healthcare information. The recent Warm Professional UI redesign (archived `2026-05-19-ui-redesign-warm-professional`) introduced English-first copy across all pages: Hero title, search placeholders, CTA buttons, footer, and page headings. However, the top navigation links (`Layout.tsx:24-26`) were left in Chinese: "首页" and "交流". This inconsistency breaks the foreign-user-first positioning.

Current state:
- **English UI**: Hero, search, buttons, sort tabs, pagination, footer, all page content
- **Chinese UI**: Navigation links in Layout only ("首页", "交流")
- **Chinese data** (by design, not UI): `nameCn` for hospitals/specialties, city/address strings from DB
- `<html lang>` attribute: likely `"zh-CN"` from the Vite scaffold default

## Goals / Non-Goals

**Goals:**
- All **UI chrome text** (labels, placeholders, buttons, headings authored by developers) SHALL be in English
- Navigation labels updated: "首页" → "Home", "交流" → "Community"
- `<html lang="en">` set correctly
- Eliminate any remaining Chinese string literals in frontend `src/` that are UI labels (not data)

**Non-Goals:**
- Internationalization (i18n) library: NOT introducing react-i18next or similar — overkill for MVP
- Chinese language version: NOT building a toggle
- Backend data fields (nameCn, address): NOT changing — these are real Chinese data, not UI labels
- Specialty emoji icons: NOT changing — data from DB

## Decisions

### D1: No i18n library — hardcoded English only
**Rationale**: The product explicitly targets English-speaking international residents. Adding i18n infra for a single-language product creates unnecessary complexity. If bilingual is needed in future, it's a separate change.
**Alternative considered**: react-i18next with json translation files → rejected (adds bundle size, dev friction, no current need).

### D2: Preserve Chinese data as content, not chrome
**Rationale**: Hospital/specialty `nameCn` is informational content for foreigners learning Chinese medical terms. Addresses are Chinese because that's the real-world location. These are NOT UI chrome.
**Rule**: If a string comes from the API/DB → leave as-is. If a string is authored in `.tsx` source → must be English.

### D3: Audit via grep, not manual scan
**Rationale**: Prevent regression. Use `grep -rP '[\x{4e00}-\x{9fff}]' frontend/src/` to find any remaining Chinese in source files, then evaluate each: data reference vs UI label.

## Risks / Trade-offs

- **[Low risk]** Some Chinese strings in `.tsx` may be inline data (like a hardcoded test hospital name) — need human judgment during grep audit. → Mitigation: only change strings that are clearly developer-authored UI labels, leave anything ambiguous.
- **[Trivial risk]** Users who actually read Chinese might lose the one familiar anchor. → Mitigation: This is the product's explicit positioning choice, not a bug.
