# Short & Sweet - Astro Site Redesign

## Context

Short & Sweet is an improv duo (Andrew Berkowitz & Hadas Cassorla) based in Portland, OR. Their current site at shortandsweet.fun is plain and needs replacing with something that reflects their playful, whimsical brand. The new site will be a single-page Astro static site hosted on Cloudflare Pages. The primary ongoing maintenance need is updating upcoming show dates, which should be as simple as possible.

## Architecture

- **Framework:** Astro (latest stable), static output mode (`output: 'static'`, the default)
- **Adapter:** None needed — Cloudflare Pages serves the static `dist/` directory directly
- **JS frameworks:** None. Pure `.astro` components + vanilla CSS
- **Show data:** `src/data/shows.yaml` — a flat YAML array, the only file that needs regular editing
- **Images:** Optimized via Astro's built-in `<Image>` component. Source images in `src/assets/images/`
- **Fonts:** Google Fonts (2 fonts max, loaded via `<link>` preconnect)
- **Build:** `npm run build` produces static output in `dist/`

### Show Data Format

```yaml
# src/data/shows.yaml
- date: 2026-02-14
  time: "9:00 PM"
  venue: Portland Comedy Theater
  location: Portland, OR
  url: https://portlandcomedy.com

- date: 2026-05-10
  time: "8:00 PM"
  venue: Curious Comedy Theater
  location: Portland, OR
  url: https://curiouscomedy.org
```

Past shows are filtered out at build time by comparing the show date against the current date (date-only comparison, no time component). Since shows are evening events, a date-only comparison in UTC is safe — a show on April 2nd won't disappear until April 3rd UTC, which is still evening of April 2nd Pacific. No client-side JS needed. The site just needs to be rebuilt/redeployed after editing the YAML (Cloudflare Pages auto-deploys on git push).

## Page Sections

Single page, top-to-bottom scroll:

1. **Hero** — Full-width section with the duo photo (`short-and-sweet-improv.jpg`, renamed from `Short & Sweet improv.jpg`), the name "Short & Sweet", subtitle "Improv and Other Tangents", and the tagline "He's short. She's sweet. They tell stories with unexpected detours."
2. **About** — Short bio paragraph with the show photo (`show.jpeg`) placed alongside
3. **Video** — Embedded YouTube player (`https://youtu.be/la_rwNhqeMk`) via a privacy-respecting lite embed or standard iframe with `loading="lazy"`
4. **Upcoming Shows** — Cards driven by `shows.yaml`, filtered to future dates only. If no upcoming shows, display a friendly "No shows scheduled yet — check back soon!" message
5. **Contact** — Email CTA linking to `hello@shortandsweet.fun`
6. **Footer** — Copyright line, site name

A minimal sticky nav at the top provides anchor links to each section. Smooth scrolling via CSS `scroll-behavior: smooth`.

## Visual Design

### Color Palette

| Role | Color | Hex |
|------|-------|-----|
| Background | Warm cream | `#FFF8F0` |
| Primary text | Deep plum | `#2D1B33` |
| Accent (links, buttons, highlights) | Hot pink/magenta | `#E91E8C` |
| Secondary accent (decorative) | Golden yellow | `#FFB627` |

### Typography

- **Headlines:** Fredoka One — rounded, bubbly, bold personality that fits the improv vibe
- **Body:** DM Sans — clean, readable
- Loaded via Google Fonts with `display=swap`

### Whimsical Touches

- Hand-drawn-style SVG section dividers (wavy lines, squiggles)
- Scattered star/sparkle SVG decorations in golden yellow
- Photos with slight rotation (2-3deg) and a soft colored drop shadow, like pinned to a corkboard
- Hero tagline in italicized, slightly larger body font
- Show cards styled as ticket stubs: dashed border on one side, slight tilt, accent color highlights
- Hover effects on interactive elements (nav links, show card URLs, contact button)

### Responsive

- Mobile-first CSS, single column on small screens
- Hero image scales down; sections stack vertically
- Nav collapses to a simple horizontal scroll or centered links on mobile
- No hamburger menu needed (only 4-5 nav items)

## File Structure

```
shortandsweet.fun/
  astro.config.mjs
  package.json
  tsconfig.json
  public/
    favicon.svg
  src/
    assets/
      images/
        short-and-sweet-improv.jpg    (renamed + moved from images/Short & Sweet improv.jpg)
        show.jpeg                      (moved from images/show.jpeg)
    components/
      Nav.astro
      Hero.astro
      About.astro
      Video.astro
      Shows.astro
      Contact.astro
      Footer.astro
      ShowCard.astro
    data/
      shows.yaml
    layouts/
      BaseLayout.astro               (head, meta, fonts, global styles)
    pages/
      index.astro                    (composes all sections)
    styles/
      global.css                     (CSS custom properties, resets, base styles)
```

## SEO & Meta Tags

`BaseLayout.astro` includes:
- `<title>Short & Sweet — Improv and Other Tangents</title>`
- `<meta name="description" content="He's short. She's sweet. They tell stories with unexpected detours. An improv duo based in Portland, OR.">`
- `og:title`, `og:description`, `og:image` (using the duo photo)
- `og:type: website`, `og:url`
- Canonical URL

## Favicon

`public/favicon.svg` — the letters "S&S" in Fredoka One, hot pink (`#E91E8C`) on a transparent background.

## Performance

- Zero JS shipped to the client by default (Astro static)
- Images optimized and converted to modern formats via `astro:assets`
- Fonts loaded with `preconnect` + `display=swap` to avoid FOIT
- YouTube embed loaded lazily
- All CSS inlined or in a single small stylesheet

## Repository Setup

- Initialize a git repo (`git init`) in the project root
- Add a `.gitignore` for Node/Astro (node_modules, dist, .astro, .DS_Store)
- Push to GitHub at the existing `shortandsweet.fun` repo
- Primary branch: `master`

## Deployment

- Cloudflare Pages, connected to the GitHub repo
- **Trigger:** auto-deploys on every push to `master`
- Build command: `npm run build`
- Output directory: `dist/`
- To update show dates: edit `src/data/shows.yaml`, commit, push to `master` — site rebuilds automatically

## Verification

1. `npm run dev` — site loads, all sections render, images display, video embeds
2. `npm run build` — builds without errors
3. Shows section correctly filters past dates (add a past date to YAML, confirm it doesn't render)
4. Responsive: check at 375px, 768px, 1200px widths
5. Lighthouse audit: aim for 95+ performance, 100 accessibility
6. Deploy preview on Cloudflare Pages
