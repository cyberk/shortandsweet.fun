# Short & Sweet Astro Site Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the current shortandsweet.fun site with a whimsical, single-page Astro static site for an improv duo, hosted on Cloudflare Pages with auto-deploy on push to master.

**Architecture:** Single-page Astro site with zero client-side JS. Show data lives in a YAML file for easy updates. All styling is vanilla CSS with CSS custom properties. No frameworks, no adapters — pure static output served by Cloudflare Pages.

**Tech Stack:** Astro (static), vanilla CSS, Google Fonts (Fredoka + DM Sans), YAML for data, Cloudflare Pages for hosting.

**Spec:** `docs/superpowers/specs/2026-04-02-astro-site-redesign-design.md`

---

## Chunk 1: Project Scaffolding & Foundation

### Task 1: Initialize git repo and scaffold Astro project

**Files:**
- Create: `package.json`, `astro.config.mjs`, `tsconfig.json`, `.gitignore`

- [ ] **Step 1: Initialize git repo**

```bash
cd /Users/andrewberkowitz/Dropbox/Mac/Documents/GitHub/shortandsweet.fun
git init
git branch -m master
```

- [ ] **Step 2: Create `.gitignore`**

```gitignore
node_modules/
dist/
.astro/
.DS_Store
```

- [ ] **Step 3: Scaffold Astro project**

```bash
npm create astro@latest . -- --template minimal --no-git --install
```

Use the minimal template, skip git init (already done), install dependencies.

- [ ] **Step 4: Verify `astro.config.mjs`**

Ensure it has `output: 'static'` (the default). No adapter needed. Add site URL:

```javascript
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://shortandsweet.fun',
});
```

- [ ] **Step 5: Run dev server to verify scaffold works**

```bash
npm run dev
```

Expected: Astro dev server starts on localhost:4321, default page loads.

- [ ] **Step 6: Commit**

```bash
git add .gitignore package.json package-lock.json astro.config.mjs tsconfig.json src/ docs/
git commit -m "chore: scaffold Astro project with minimal template"
```

### Task 2: Move and rename images

**Files:**
- Create: `src/assets/images/short-and-sweet-improv.jpg`, `src/assets/images/show.jpeg`
- Delete: `images/` directory (after move)

- [ ] **Step 1: Create directory and move images**

```bash
mkdir -p src/assets/images
cp "images/Short & Sweet improv.jpg" src/assets/images/short-and-sweet-improv.jpg
cp images/show.jpeg src/assets/images/show.jpeg
```

- [ ] **Step 2: Remove old images directory**

```bash
rm -rf images/
```

- [ ] **Step 3: Commit**

```bash
git add src/assets/images/ .gitignore
git rm -r --cached images/ 2>/dev/null || true
git commit -m "chore: move images to src/assets/images, rename to kebab-case"
```

### Task 3: Create global styles and CSS custom properties

**Files:**
- Create: `src/styles/global.css`

- [ ] **Step 1: Create `src/styles/global.css`**

```css
/* CSS Custom Properties */
:root {
  --color-bg: #FFF8F0;
  --color-text: #2D1B33;
  --color-accent: #E91E8C;
  --color-accent-yellow: #FFB627;
  --font-display: 'Fredoka', cursive;
  --font-body: 'DM Sans', sans-serif;
}

/* Reset */
*, *::before, *::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

html {
  scroll-behavior: smooth;
}

body {
  font-family: var(--font-body);
  color: var(--color-text);
  background-color: var(--color-bg);
  line-height: 1.6;
}

img {
  max-width: 100%;
  height: auto;
  display: block;
}

a {
  color: var(--color-accent);
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}

h1, h2, h3 {
  font-family: var(--font-display);
  line-height: 1.2;
}

/* Section spacing */
section {
  padding: 4rem 1.5rem;
}

@media (min-width: 768px) {
  section {
    padding: 6rem 2rem;
  }
}

.container {
  max-width: 900px;
  margin: 0 auto;
}

/* Section heading style */
.section-heading {
  font-size: 2rem;
  text-align: center;
  margin-bottom: 2rem;
  color: var(--color-accent);
}
```

- [ ] **Step 2: Commit**

```bash
git add src/styles/global.css
git commit -m "feat: add global CSS with design tokens and reset"
```

### Task 4: Create BaseLayout with fonts and meta tags

**Files:**
- Create: `src/layouts/BaseLayout.astro`

- [ ] **Step 1: Create `src/layouts/BaseLayout.astro`**

This layout includes Google Fonts preconnect, meta tags, OG tags, and imports global.css.

```astro
---
import '../styles/global.css';

const title = 'Short & Sweet — Improv and Other Tangents';
const description = "He's short. She's sweet. They tell stories with unexpected detours. An improv duo based in Portland, OR.";
const siteUrl = 'https://shortandsweet.fun';
const ogImage = `${siteUrl}/og-image.jpg`;
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>{title}</title>
    <meta name="description" content={description} />
    <link rel="canonical" href={siteUrl} />

    <!-- Open Graph -->
    <meta property="og:type" content="website" />
    <meta property="og:url" content={siteUrl} />
    <meta property="og:title" content={title} />
    <meta property="og:description" content={description} />
    <meta property="og:image" content={ogImage} />

    <!-- Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com" />
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
    <link
      href="https://fonts.googleapis.com/css2?family=Fredoka:wght@700&family=DM+Sans:ital,wght@0,400;0,500;0,700;1,400&display=swap"
      rel="stylesheet"
    />

    <!-- Favicon -->
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
  </head>
  <body>
    <slot />
  </body>
</html>
```

- [ ] **Step 2: Commit**

```bash
git add src/layouts/BaseLayout.astro
git commit -m "feat: add BaseLayout with fonts, meta tags, and OG tags"
```

### Task 5: Create favicon

**Files:**
- Create: `public/favicon.svg`

Note: Wavy dividers and sparkles are inlined directly in the Hero and About components for better performance, so no separate SVG files are needed.

- [ ] **Step 1: Create `public/favicon.svg`**

Pink "S&S" text on transparent background.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 64 64">
  <text x="50%" y="55%" text-anchor="middle" dominant-baseline="middle" font-family="sans-serif" font-weight="bold" font-size="22" fill="#E91E8C">S&amp;S</text>
</svg>
```

- [ ] **Step 2: Commit**

```bash
git add public/favicon.svg
git commit -m "feat: add favicon"
```

---

## Chunk 2: Page Components

### Task 6: Create Nav component

**Files:**
- Create: `src/components/Nav.astro`

- [ ] **Step 1: Create `src/components/Nav.astro`**

Sticky nav with anchor links. Minimal, centered on mobile.

```astro
---
const links = [
  { href: '#about', label: 'About' },
  { href: '#video', label: 'Video' },
  { href: '#shows', label: 'Shows' },
  { href: '#contact', label: 'Contact' },
];
---

<nav class="nav">
  <a href="#" class="nav-logo">Short & Sweet</a>
  <ul class="nav-links">
    {links.map(link => (
      <li><a href={link.href}>{link.label}</a></li>
    ))}
  </ul>
</nav>

<style>
  .nav {
    position: sticky;
    top: 0;
    z-index: 100;
    background: var(--color-bg);
    border-bottom: 2px solid var(--color-accent);
    padding: 0.75rem 1.5rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 0.5rem;
  }

  .nav-logo {
    font-family: var(--font-display);
    font-size: 1.25rem;
    color: var(--color-accent);
    text-decoration: none;
  }

  .nav-links {
    list-style: none;
    display: flex;
    gap: 1.5rem;
    flex-wrap: wrap;
    justify-content: center;
  }

  .nav-links a {
    font-weight: 500;
    font-size: 0.95rem;
    transition: color 0.2s;
  }

  .nav-links a:hover {
    color: var(--color-accent-yellow);
    text-decoration: none;
  }
</style>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Nav.astro
git commit -m "feat: add Nav component with sticky positioning and anchor links"
```

### Task 7: Create Hero component

**Files:**
- Create: `src/components/Hero.astro`

- [ ] **Step 1: Create `src/components/Hero.astro`**

Full-width hero with the duo photo (rotated, pinned style), title, subtitle, and tagline. Sparkle decorations.

```astro
---
import { Image } from 'astro:assets';
import heroImage from '../assets/images/short-and-sweet-improv.jpg';
---

<section class="hero">
  <div class="hero-content">
    <div class="hero-image-wrapper">
      <Image src={heroImage} alt="Andrew and Hadas — the Short & Sweet improv duo" width={500} class="hero-image" />
    </div>
    <div class="hero-text">
      <h1 class="hero-title">Short <span class="amp">&</span> Sweet</h1>
      <p class="hero-subtitle">Improv and Other Tangents</p>
      <p class="hero-tagline">He's short. She's sweet. They tell stories with unexpected detours.</p>
    </div>
  </div>
  <div class="sparkle sparkle-1" aria-hidden="true"></div>
  <div class="sparkle sparkle-2" aria-hidden="true"></div>
  <div class="sparkle sparkle-3" aria-hidden="true"></div>
</section>

<style>
  .hero {
    position: relative;
    padding: 4rem 1.5rem 3rem;
    text-align: center;
    overflow: hidden;
  }

  .hero-content {
    max-width: 900px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2rem;
  }

  .hero-image-wrapper {
    transform: rotate(-2deg);
    box-shadow: 6px 6px 0 var(--color-accent-yellow), 8px 8px 20px rgba(0,0,0,0.1);
    border: 4px solid white;
    border-radius: 4px;
    overflow: hidden;
    max-width: 400px;
  }

  .hero-image {
    display: block;
    width: 100%;
    height: auto;
  }

  .hero-title {
    font-size: 3.5rem;
    color: var(--color-text);
  }

  .amp {
    color: var(--color-accent);
  }

  .hero-subtitle {
    font-family: var(--font-display);
    font-size: 1.3rem;
    color: var(--color-accent);
    margin-top: 0.25rem;
  }

  .hero-tagline {
    font-style: italic;
    font-size: 1.2rem;
    margin-top: 1rem;
    max-width: 500px;
  }

  /* Sparkle decorations */
  .sparkle {
    position: absolute;
    width: 24px;
    height: 24px;
    background: var(--color-accent-yellow);
    clip-path: polygon(50% 0%, 58% 38%, 100% 50%, 58% 62%, 50% 100%, 42% 62%, 0% 50%, 42% 38%);
  }

  .sparkle-1 { top: 10%; left: 8%; width: 20px; height: 20px; }
  .sparkle-2 { top: 20%; right: 10%; width: 28px; height: 28px; }
  .sparkle-3 { bottom: 15%; left: 15%; width: 16px; height: 16px; }

  @media (min-width: 768px) {
    .hero-content {
      flex-direction: row;
      text-align: left;
    }
    .hero-title {
      font-size: 4.5rem;
    }
  }
</style>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Hero.astro
git commit -m "feat: add Hero component with pinned photo style and sparkles"
```

### Task 8: Create About component

**Files:**
- Create: `src/components/About.astro`

- [ ] **Step 1: Create `src/components/About.astro`**

Short bio with the show photo alongside, wavy divider above.

```astro
---
import { Image } from 'astro:assets';
import showImage from '../assets/images/show.jpeg';
---

<div class="divider" aria-hidden="true">
  <svg viewBox="0 0 1200 60" fill="none" preserveAspectRatio="none">
    <path d="M0,30 Q150,0 300,30 T600,30 T900,30 T1200,30" stroke="#E91E8C" stroke-width="3" fill="none" stroke-linecap="round"/>
  </svg>
</div>

<section id="about" class="about">
  <div class="about-inner">
    <div class="about-image-wrapper">
      <Image src={showImage} alt="Short & Sweet performing live on stage" width={450} class="about-image" />
    </div>
    <div class="about-text">
      <h2 class="section-heading">About Us</h2>
      <p>
        Short & Sweet is Andrew Berkowitz and Hadas Cassorla — an improv duo based in Portland, OR
        (and sometimes found around the world). They create spontaneous stories full of unexpected
        detours, brought to life with chemistry, quick thinking, and a whole lot of heart.
      </p>
    </div>
  </div>
</section>

<style>
  .divider {
    width: 100%;
    height: 40px;
    overflow: hidden;
  }

  .divider svg {
    width: 100%;
    height: 100%;
  }

  .about {
    padding-top: 2rem;
  }

  .about-inner {
    max-width: 900px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 2rem;
  }

  .about-image-wrapper {
    transform: rotate(2deg);
    box-shadow: -4px 4px 0 var(--color-accent), -6px 6px 16px rgba(0,0,0,0.08);
    border: 4px solid white;
    border-radius: 4px;
    overflow: hidden;
    max-width: 400px;
    flex-shrink: 0;
  }

  .about-image {
    display: block;
    width: 100%;
    height: auto;
  }

  .about-text {
    max-width: 450px;
  }

  .about-text .section-heading {
    text-align: left;
  }

  .about-text p {
    font-size: 1.1rem;
    line-height: 1.7;
  }

  @media (min-width: 768px) {
    .about-inner {
      flex-direction: row;
    }
  }
</style>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/About.astro
git commit -m "feat: add About component with rotated photo and bio"
```

### Task 9: Create Video component

**Files:**
- Create: `src/components/Video.astro`

- [ ] **Step 1: Create `src/components/Video.astro`**

Responsive YouTube embed with `loading="lazy"` and privacy-enhanced mode.

```astro
<section id="video" class="video-section">
  <div class="container">
    <h2 class="section-heading">Watch Us</h2>
    <div class="video-wrapper">
      <iframe
        src="https://www.youtube-nocookie.com/embed/la_rwNhqeMk"
        title="Short & Sweet improv performance"
        loading="lazy"
        allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
        allowfullscreen
      ></iframe>
    </div>
  </div>
</section>

<style>
  .video-section {
    text-align: center;
  }

  .video-wrapper {
    position: relative;
    width: 100%;
    max-width: 720px;
    margin: 0 auto;
    padding-bottom: 56.25%; /* 16:9 */
    height: 0;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 4px 4px 0 var(--color-accent-yellow);
  }

  .video-wrapper iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border: 0;
  }
</style>
```

- [ ] **Step 2: Commit**

```bash
git add src/components/Video.astro
git commit -m "feat: add Video component with lazy-loaded YouTube embed"
```

### Task 10: Create ShowCard and Shows components

**Files:**
- Create: `src/components/ShowCard.astro`, `src/components/Shows.astro`, `src/data/shows.yaml`

- [ ] **Step 1: Create `src/data/shows.yaml`**

Seed with one future show and one past show (for testing filtering).

```yaml
- date: 2026-02-14
  time: "9:00 PM"
  venue: Portland Comedy Theater
  location: Portland, OR
  url: https://portlandcomedy.com

- date: 2027-06-15
  time: "8:00 PM"
  venue: Curious Comedy Theater
  location: Portland, OR
  url: https://curiouscomedy.org
```

- [ ] **Step 2: Create `src/components/ShowCard.astro`**

Ticket-stub styled card for a single show.

```astro
---
interface Props {
  date: string;
  time: string;
  venue: string;
  location: string;
  url?: string;
}

const { date, time, venue, location, url } = Astro.props;

const formatted = new Date(date + 'T00:00:00').toLocaleDateString('en-US', {
  weekday: 'long',
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});
---

<div class="show-card">
  <div class="show-card-date">
    <span class="show-day">{formatted}</span>
    <span class="show-time">{time}</span>
  </div>
  <div class="show-card-details">
    <h3 class="show-venue">{venue}</h3>
    <p class="show-location">{location}</p>
    {url && <a href={url} target="_blank" rel="noopener noreferrer" class="show-link">Tickets & Info &rarr;</a>}
  </div>
</div>

<style>
  .show-card {
    display: flex;
    flex-direction: column;
    border: 2px solid var(--color-accent);
    border-left: 6px dashed var(--color-accent);
    border-radius: 0 8px 8px 0;
    padding: 1.25rem 1.5rem;
    transform: rotate(-0.5deg);
    background: white;
    box-shadow: 3px 3px 0 var(--color-accent-yellow);
    transition: transform 0.2s;
  }

  .show-card:hover {
    transform: rotate(0deg) translateY(-2px);
  }

  .show-card-date {
    font-family: var(--font-display);
    margin-bottom: 0.5rem;
  }

  .show-day {
    font-size: 1.1rem;
    color: var(--color-accent);
    display: block;
  }

  .show-time {
    font-size: 0.9rem;
    color: var(--color-text);
    opacity: 0.7;
  }

  .show-venue {
    font-family: var(--font-body);
    font-size: 1.15rem;
    font-weight: 700;
  }

  .show-location {
    font-size: 0.95rem;
    opacity: 0.7;
    margin-top: 0.15rem;
  }

  .show-link {
    display: inline-block;
    margin-top: 0.5rem;
    font-weight: 700;
    font-size: 0.95rem;
  }

  @media (min-width: 768px) {
    .show-card {
      flex-direction: row;
      align-items: center;
      gap: 2rem;
    }

    .show-card-date {
      margin-bottom: 0;
      min-width: 200px;
    }
  }
</style>
```

- [ ] **Step 3: Create `src/components/Shows.astro`**

Reads YAML, filters to future shows, renders ShowCard list or empty-state message.

```astro
---
import yaml from 'js-yaml';
import fs from 'node:fs';
import ShowCard from './ShowCard.astro';

const yamlPath = new URL('../data/shows.yaml', import.meta.url);
const raw = fs.readFileSync(yamlPath, 'utf-8');
const allShows = yaml.load(raw) as Array<{ date: string; time: string; venue: string; location: string; url?: string }>;

const today = new Date().toISOString().split('T')[0];
const upcomingShows = allShows
  .filter(show => show.date >= today)
  .sort((a, b) => a.date.localeCompare(b.date));
---

<section id="shows" class="shows-section">
  <div class="container">
    <h2 class="section-heading">Upcoming Shows</h2>
    {upcomingShows.length > 0 ? (
      <div class="shows-list">
        {upcomingShows.map(show => (
          <ShowCard {...show} />
        ))}
      </div>
    ) : (
      <p class="no-shows">No shows scheduled yet — check back soon!</p>
    )}
  </div>
</section>

<style>
  .shows-list {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
    max-width: 650px;
    margin: 0 auto;
  }

  .no-shows {
    text-align: center;
    font-style: italic;
    font-size: 1.1rem;
    opacity: 0.7;
  }
</style>
```

- [ ] **Step 4: Install `js-yaml` dependency (with types)**

```bash
npm install js-yaml
npm install -D @types/js-yaml
```

- [ ] **Step 5: Commit**

```bash
git add src/data/shows.yaml src/components/ShowCard.astro src/components/Shows.astro package.json package-lock.json
git commit -m "feat: add Shows section with YAML data, ticket-stub cards, and date filtering"
```

### Task 11: Create Contact and Footer components

**Files:**
- Create: `src/components/Contact.astro`, `src/components/Footer.astro`

- [ ] **Step 1: Create `src/components/Contact.astro`**

```astro
<section id="contact" class="contact-section">
  <div class="container">
    <h2 class="section-heading">Get In Touch</h2>
    <p class="contact-text">Want to book us? Have a question? Just want to say hi?</p>
    <a href="mailto:hello@shortandsweet.fun" class="contact-button">
      hello@shortandsweet.fun
    </a>
  </div>
</section>

<style>
  .contact-section {
    text-align: center;
  }

  .contact-text {
    font-size: 1.1rem;
    margin-bottom: 1.5rem;
  }

  .contact-button {
    display: inline-block;
    background: var(--color-accent);
    color: white;
    font-family: var(--font-display);
    font-size: 1.1rem;
    padding: 0.75rem 2rem;
    border-radius: 50px;
    text-decoration: none;
    box-shadow: 3px 3px 0 var(--color-accent-yellow);
    transition: transform 0.2s, box-shadow 0.2s;
  }

  .contact-button:hover {
    transform: translateY(-2px);
    box-shadow: 4px 5px 0 var(--color-accent-yellow);
    text-decoration: none;
  }
</style>
```

- [ ] **Step 2: Create `src/components/Footer.astro`**

```astro
---
const year = new Date().getFullYear();
---

<footer class="footer">
  <p>&copy; {year} Short & Sweet &middot; <a href="https://shortandsweet.fun">shortandsweet.fun</a></p>
</footer>

<style>
  .footer {
    text-align: center;
    padding: 2rem 1.5rem;
    font-size: 0.85rem;
    opacity: 0.6;
    border-top: 1px solid var(--color-accent);
  }

  .footer a {
    color: var(--color-text);
  }
</style>
```

- [ ] **Step 3: Commit**

```bash
git add src/components/Contact.astro src/components/Footer.astro
git commit -m "feat: add Contact and Footer components"
```

---

## Chunk 3: Page Assembly & Verification

### Task 12: Assemble index page

**Files:**
- Create: `src/pages/index.astro`
- Delete: default `src/pages/index.astro` from scaffold (overwrite)

- [ ] **Step 1: Create `src/pages/index.astro`**

Compose all sections together using the BaseLayout.

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Nav from '../components/Nav.astro';
import Hero from '../components/Hero.astro';
import About from '../components/About.astro';
import Video from '../components/Video.astro';
import Shows from '../components/Shows.astro';
import Contact from '../components/Contact.astro';
import Footer from '../components/Footer.astro';
---

<BaseLayout>
  <Nav />
  <main>
    <Hero />
    <About />
    <Video />
    <Shows />
    <Contact />
  </main>
  <Footer />
</BaseLayout>
```

- [ ] **Step 2: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: assemble index page with all sections"
```

### Task 13: Generate OG image

**Files:**
- Create: `public/og-image.jpg`

- [ ] **Step 1: Create a resized OG image from the duo photo**

Resize the hero image to 1200px wide (standard OG image size) for social sharing. Use `sips` (built-in macOS tool):

```bash
sips -Z 1200 --setProperty format jpeg src/assets/images/short-and-sweet-improv.jpg --out public/og-image.jpg
```

- [ ] **Step 2: Commit**

```bash
git add public/og-image.jpg
git commit -m "feat: add OG image for social sharing"
```

### Task 14: Verify dev build

- [ ] **Step 1: Start dev server and verify all sections**

```bash
npm run dev
```

Open in browser. Verify:
- Nav is sticky with working anchor links
- Hero shows duo photo with rotation/shadow, title, tagline
- About shows show photo with bio text
- Video plays YouTube embed
- Shows section displays future shows only (past show filtered out)
- Contact has email button
- Footer shows copyright
- Smooth scrolling between sections

- [ ] **Step 2: Test responsive at 375px, 768px, 1200px**

Check layout stacking, font sizes, image scaling at each breakpoint.

- [ ] **Step 3: Run production build**

```bash
npm run build
```

Expected: Clean build, no errors, output in `dist/`.

- [ ] **Step 4: Preview production build**

```bash
npm run preview
```

Verify the production build serves correctly.

### Task 15: Polish and final adjustments

- [ ] **Step 1: Review and fix any visual issues**

Iterate on spacing, colors, responsive behavior based on what the dev build looks like. Adjust CSS as needed.

- [ ] **Step 2: Commit any polish**

```bash
git add -A
git commit -m "fix: polish spacing, responsive layout, and visual tweaks"
```

### Task 16: Push to GitHub and set up Cloudflare Pages

- [ ] **Step 1: Push to GitHub**

```bash
git remote add origin git@github.com:andrewberkowitz/shortandsweet.fun.git
git push -u origin master
```

(Adjust remote URL if needed.)

- [ ] **Step 2: Configure Cloudflare Pages**

In Cloudflare dashboard:
- Connect GitHub repo
- Build command: `npm run build`
- Output directory: `dist/`
- Branch: `master`

- [ ] **Step 3: Verify live deployment**

Visit https://shortandsweet.fun after deployment completes. Run Lighthouse audit, aim for 95+ performance, 100 accessibility.
