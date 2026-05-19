## Context

The frontend is built with React 19 + TypeScript + Vite 6 + Tailwind CSS v4. Currently it uses a minimal "developer template" aesthetic — gray-50 backgrounds, system fonts, blue-only accent color, tight spacing, and no visual personality.

The target audience is international users (foreigners living in China) looking for trustworthy medical information. Visual credibility is critical for this audience.

Current state:
- 7 page components + 1 Layout component
- Tailwind v4 (CSS-based config via `@import "tailwindcss"`)
- No custom fonts, no icon library, no design tokens
- All styles are inline Tailwind utility classes

## Goals / Non-Goals

**Goals:**
- Establish a "Warm Professional" design language — warm tones, geometric precision, generous whitespace
- Create a cohesive color system (teal primary, amber accent, warm slate neutrals)
- Introduce Inter font for professional typography
- Replace emoji icons with Lucide React for consistency
- Restructure Layout with sticky nav, proper navigation, and rich footer
- Add Hero section to homepage for strong first impression
- Improve information hierarchy across all pages
- Maintain all existing functionality — this is a visual-only refactor

**Non-Goals:**
- Dark mode (future consideration)
- Component library extraction (premature at this scale)
- Animation framework (keep to CSS transitions)
- Responsive redesign beyond current breakpoints (already responsive via Tailwind)
- Backend changes of any kind

## Decisions

### D1: Color Palette — Teal + Amber on Warm Neutrals

**Choice:** Teal-700 (`#0f766e`) as primary, Amber-600 (`#d97706`) as accent, warm off-white (`#faf9f7`) as page background.

**Rationale:** Teal conveys trust and professionalism (common in healthcare), amber adds warmth without being playful. The warm off-white background avoids the cold sterility of pure gray-50.

**Alternatives considered:**
- Blue (current) — too generic, doesn't differentiate
- Green — too "eco/wellness", less professional
- Indigo — elegant but cold, doesn't match warm direction

### D2: Typography — Inter via Google Fonts CDN

**Choice:** Load Inter from Google Fonts CDN, apply as primary font-family.

**Rationale:** Inter is the de facto standard for modern Western UI design. It has excellent legibility at all sizes, wide language support, and free availability. CDN delivery means no build-time bundling.

**Alternatives considered:**
- @fontsource/inter (npm) — heavier bundle, more complex setup
- DM Sans — slightly more personality but less proven at scale
- System fonts (current) — inconsistent cross-platform rendering

### D3: Icon System — Lucide React

**Choice:** `lucide-react` package for all icons.

**Rationale:** Tree-shakeable, consistent 24px grid, large icon set, MIT licensed, actively maintained. Replaces emoji usage (🏥 👍 💬 ⭐ etc.) which renders differently across platforms.

**Alternatives considered:**
- Heroicons — good but smaller set
- React Icons (umbrella) — larger bundle, inconsistent styles
- Custom SVGs — maintenance burden at MVP stage

### D4: Design Tokens via Tailwind v4 CSS Variables

**Choice:** Define design tokens as CSS custom properties in `index.css`, referenced by Tailwind's `@theme` directive.

**Rationale:** Tailwind v4 natively supports CSS-based configuration. This keeps tokens co-located, easy to change, and avoids a separate config file. Tokens include colors, border-radius, shadows, and font stacks.

```css
@import "tailwindcss";

@theme {
  --color-primary: #0f766e;
  --color-primary-light: #f0fdfa;
  --color-accent: #d97706;
  --color-accent-light: #fffbeb;
  --color-surface: #ffffff;
  --color-background: #faf9f7;
  --color-border: #e8e5e0;
  --font-sans: 'Inter', system-ui, sans-serif;
  --radius-card: 1rem;
  --shadow-card: 0 1px 3px 0 rgb(0 0 0 / 0.05), 0 1px 2px -1px rgb(0 0 0 / 0.05);
  --shadow-card-hover: 0 10px 15px -3px rgb(0 0 0 / 0.08), 0 4px 6px -4px rgb(0 0 0 / 0.05);
}
```

### D5: Layout Structure — Sticky Header + Hero + Footer

**Choice:**
- Header: sticky, white background, blur backdrop, Logo + nav links + search
- Hero (homepage only): warm gradient background, large typography, centered search
- Footer: 3-column layout with links, description, and copyright

**Rationale:** This is the standard Western SaaS/directory layout pattern. Sticky header provides constant navigation access. Hero creates a strong landing impression. Rich footer adds credibility.

### D6: Card Pattern — Shadow-based Depth, No Borders

**Choice:** Cards use `rounded-2xl` + `shadow-card` instead of `border + shadow-sm`. On hover, elevate to `shadow-card-hover` with subtle `translate-y` transition.

**Rationale:** Borderless cards with shadow feel more premium and modern (Stripe, Linear pattern). The warm background provides natural contrast without needing explicit borders.

## Risks / Trade-offs

- **[Performance] Google Fonts CDN** → May cause FOUT (flash of unstyled text). Mitigation: `font-display: swap` + preconnect link.
- **[Bundle size] Lucide React** → Adds ~2-5KB per icon used (tree-shaking). Mitigation: only import icons actually used, verify build output.
- **[Consistency] Full rewrite risk** → Rewriting all pages risks introducing bugs. Mitigation: Verify build compiles and manually check each page after changes.
- **[Tailwind v4 @theme support]** → Tailwind v4 is relatively new, `@theme` directive behavior may differ from docs. Mitigation: test incrementally, fallback to CSS variables with `var()` directly if needed.

## Open Questions

- Should specialty icons remain as emoji (they're data-driven from backend) or should we map them to Lucide icons? **Decision: Keep emoji for specialty icons** (they're stored in DB), replace only UI-chrome emoji (like 👍💬⭐).
