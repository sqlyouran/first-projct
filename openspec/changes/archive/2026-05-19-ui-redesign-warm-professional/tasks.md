## 1. Foundation — Dependencies & Design Tokens

- [x] 1.1 Install `lucide-react` package
- [x] 1.2 Add Inter font links (preconnect + stylesheet) to `index.html`
- [x] 1.3 Configure design tokens in `index.css` using Tailwind v4 `@theme` directive (colors, fonts, shadows, radius)
- [x] 1.4 Verify Tailwind picks up new tokens — build passes with no errors

## 2. Layout — Header, Footer, Shell

- [x] 2.1 Rewrite `Layout.tsx` — sticky header with blur backdrop, logo, nav links (首页/科室/交流), search input, active link highlighting
- [x] 2.2 Add rich footer to `Layout.tsx` — 3-column layout (brand+description, links, copyright)
- [x] 2.3 Update page background to warm off-white (`bg-background`), content max-width to `max-w-7xl`
- [x] 2.4 Verify layout renders correctly — build passes, visual check

## 3. HomePage — Hero Section & Specialty Grid

- [x] 3.1 Add hero section to `HomePage.tsx` — warm gradient background, large title, subtitle, centered search bar
- [x] 3.2 Redesign specialty card grid — bigger cards, more whitespace, shadow-based depth, hover elevation
- [x] 3.3 Replace any emoji in UI chrome with Lucide icons (keep specialty emoji from DB)
- [x] 3.4 Verify homepage renders — build passes

## 4. SpecialtyRankingPage — Table & Visual Hierarchy

- [x] 4.1 Redesign page header with better typography hierarchy and back navigation
- [x] 4.2 Upgrade ranking table — borderless card container, better row spacing, refined rank badges
- [x] 4.3 Restyle city filter dropdown with new design tokens
- [x] 4.4 Upgrade related posts section styling
- [x] 4.5 Verify ranking page — build passes

## 5. HospitalDetailPage — Information Cards

- [x] 5.1 Redesign hospital header section (name, international badge) with new tokens
- [x] 5.2 Upgrade info grid cards (location, contact) — Lucide icons, better spacing
- [x] 5.3 Restyle top specialties grid and related posts section
- [x] 5.4 Verify hospital detail page — build passes

## 6. Community Pages — Posts & Interactions

- [x] 6.1 Redesign `CommunityPage.tsx` — post cards with new card pattern, improved sort tabs (pill style with primary color)
- [x] 6.2 Redesign `PostDetailPage.tsx` — content card, interaction buttons with Lucide icons (ThumbsUp, Star, MessageCircle), comment section
- [x] 6.3 Redesign `NewPostPage.tsx` — form layout with better spacing, inputs, and chip selectors
- [x] 6.4 Replace all emoji in community pages with Lucide icons (👍→ThumbsUp, 💬→MessageCircle, ⭐→Star, 🏥→Hospital)
- [x] 6.5 Verify all community pages — build passes

## 7. SearchPage — Consistent Styling

- [x] 7.1 Apply new card pattern and design tokens to `SearchPage.tsx`
- [x] 7.2 Verify search page — build passes

## 8. Final Verification

- [x] 8.1 Run `npm run build` — zero errors, zero warnings
- [x] 8.2 Visual sanity check — all pages consistent with Warm Professional design direction
