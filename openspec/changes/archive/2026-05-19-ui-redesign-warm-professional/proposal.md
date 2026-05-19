## Why

The current frontend uses a generic "developer template" style — flat gray backgrounds, weak card borders, system fonts, and minimal visual hierarchy. It feels functional but visually unrefined, especially for a product targeting international users (foreigners in China) who expect a polished, trustworthy medical information platform. A professional redesign will significantly improve perceived credibility and user engagement.

## What Changes

- **Complete visual overhaul** of all frontend pages (Layout, Home, SpecialtyRanking, HospitalDetail, Community, PostDetail, NewPost)
- **New design system** based on "Warm Professional" direction — warm tones (teal/amber), geometric precision, generous whitespace
- **Typography upgrade** — introduce Inter font via Google Fonts CDN
- **Icon system** — replace emoji icons with Lucide React icon library
- **Layout restructure** — sticky header with full navigation, hero section on homepage, multi-column footer
- **Color palette** — warm off-white backgrounds, teal primary, amber accent, slate text hierarchy
- **Card system** — larger border-radius (2xl), shadow-based depth instead of borders
- **Spacing system** — more generous padding/margins for breathing room

## Capabilities

### New Capabilities
- `design-system`: Global design tokens (colors, typography, spacing, shadows), Tailwind theme customization, and shared UI patterns
- `page-layouts`: Restructured page layouts including sticky header, hero section, content areas, and footer

### Modified Capabilities
_None — this is a pure frontend visual change with no spec-level behavior modifications._

## Impact

- **Frontend only** — no backend changes needed
- **Dependencies**: Add `lucide-react` package, Inter font via CDN
- **Files affected**: All `.tsx` files in `src/components/` and `src/pages/`, `index.css`, `index.html` (font link)
- **Tailwind config**: May need `tailwind.config` or CSS custom properties for design tokens (Tailwind v4 uses CSS-based config)
- **No API changes** — all existing functionality preserved, only visual presentation changes
