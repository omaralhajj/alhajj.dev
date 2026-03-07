# Portfolio Website Design — alhajj.dev

**Date:** 2026-03-07
**Status:** Approved

## Overview

A single-page personal portfolio website for a software engineer. Minimal and clean aesthetic, easily extendible for future sections or pages. Deployed to Netlify as a static site.

## Stack

- **Framework:** Astro
- **Styling:** Tailwind CSS (`@astrojs/tailwind`)
- **Fonts:** Inter (body), JetBrains Mono (code/labels) via Fontsource
- **Deployment:** Netlify (static export)

## Page Sections

In order of appearance:

1. **Header** — Sticky. Name on the left, nav links + dark/light toggle on the right.
2. **Hero** — Name, title/role, one-liner bio.
3. **About** — Expanded bio paragraph.
4. **Skills** — Tech skill tags in a grid. Monospace font styling.
5. **Experience** — Work history in a vertical timeline.
6. **Projects** — Cards with title, description, tech stack tags, and link.
7. **Contact** — Links to email, GitHub, LinkedIn.
8. **Footer** — Minimal, just copyright.

All content is placeholder text to be filled in by the owner.

## Visual Design

- **Light mode background:** `#fafafa`, dark text
- **Dark mode background:** `#111`, light text
- **Accent color:** Muted indigo (used for links, hover states, active nav)
- **Theme toggle:** Sun/moon icon in the header; preference persisted via `localStorage`; no flash on load (inline script in `<head>`)
- **Max content width:** ~680px, centered
- **Spacing:** Generous vertical padding between sections
- **Animations:** Subtle fade-in on scroll for each section, nothing else

## Project Structure

```
src/
  components/
    Header.astro
    Hero.astro
    About.astro
    Skills.astro
    Experience.astro
    Projects.astro
    Contact.astro
    Footer.astro
  layouts/
    Layout.astro
  pages/
    index.astro
  styles/
    global.css
public/
  favicon.svg
docs/
  plans/
astro.config.mjs
tailwind.config.mjs
netlify.toml
package.json
```

## Extensibility

- New pages are added by creating files in `src/pages/`
- New sections are Astro components dropped into `index.astro`
- Blog support can be added later via Astro's content collections
- Netlify handles CI/CD — pushing to main deploys automatically
