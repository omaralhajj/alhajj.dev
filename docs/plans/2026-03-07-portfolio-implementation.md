# Portfolio Website Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a single-page personal portfolio for alhajj.dev with About, Skills, Experience, Projects, and Contact sections.

**Architecture:** Astro static site composed of isolated Astro components, one per section, assembled in a single `index.astro` page. Dark/light mode is handled via a CSS custom property theme system (`data-theme` on `<html>`) toggled by a small inline script that reads `localStorage` — this prevents any flash on load. Tailwind is configured in `class` dark mode strategy.

**Tech Stack:** Astro, Tailwind CSS (`@astrojs/tailwind`), Fontsource (Inter + JetBrains Mono), Netlify

---

### Task 1: Initialize Astro project with Tailwind

**Files:**
- Create: `package.json`, `astro.config.mjs`, `tailwind.config.mjs`, `tsconfig.json` (all generated)

**Step 1: Scaffold the Astro project**

Run from `C:/Users/Omar/Udvikler/Code/alhajj-dev`:

```bash
npm create astro@latest . -- --template minimal --install --git --typescript strict
```

When prompted:
- Template: minimal
- Install dependencies: yes
- Initialize git repo: yes

**Step 2: Add Tailwind integration**

```bash
npx astro add tailwind --yes
```

**Step 3: Install fonts**

```bash
npm install @fontsource/inter @fontsource/jetbrains-mono
```

**Step 4: Verify build works**

```bash
npm run build
```

Expected: `dist/` folder created, no errors.

**Step 5: Commit**

```bash
git add -A
git commit -m "feat: initialize Astro project with Tailwind and Fontsource fonts"
```

---

### Task 2: Configure Tailwind and global styles

**Files:**
- Modify: `tailwind.config.mjs`
- Create: `src/styles/global.css`

**Step 1: Update `tailwind.config.mjs`**

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./src/**/*.{astro,html,js,jsx,md,mdx,svelte,ts,tsx,vue}'],
  darkMode: 'class',
  theme: {
    extend: {
      colors: {
        indigo: {
          muted: '#6366f1',
        },
      },
      fontFamily: {
        sans: ['Inter', 'sans-serif'],
        mono: ['JetBrains Mono', 'monospace'],
      },
    },
  },
  plugins: [],
}
```

**Step 2: Create `src/styles/global.css`**

```css
@import '@fontsource/inter/400.css';
@import '@fontsource/inter/500.css';
@import '@fontsource/inter/600.css';
@import '@fontsource/jetbrains-mono/400.css';

@tailwind base;
@tailwind components;
@tailwind utilities;

:root {
  --color-bg: #fafafa;
  --color-text: #111111;
  --color-text-muted: #6b7280;
  --color-border: #e5e7eb;
  --color-accent: #6366f1;
  --color-card: #ffffff;
}

:root.dark {
  --color-bg: #111111;
  --color-text: #f5f5f5;
  --color-text-muted: #9ca3af;
  --color-border: #262626;
  --color-accent: #818cf8;
  --color-card: #1a1a1a;
}

html {
  scroll-behavior: smooth;
}

body {
  background-color: var(--color-bg);
  color: var(--color-text);
  font-family: 'Inter', sans-serif;
  transition: background-color 0.2s ease, color 0.2s ease;
}

/* Fade-in animation for sections */
.fade-in {
  opacity: 0;
  transform: translateY(16px);
  transition: opacity 0.5s ease, transform 0.5s ease;
}

.fade-in.visible {
  opacity: 1;
  transform: translateY(0);
}
```

**Step 3: Verify build**

```bash
npm run build
```

Expected: no errors.

**Step 4: Commit**

```bash
git add -A
git commit -m "feat: configure Tailwind dark mode and global CSS theme variables"
```

---

### Task 3: Create Layout.astro

**Files:**
- Create: `src/layouts/Layout.astro`

**Step 1: Write `src/layouts/Layout.astro`**

```astro
---
import '../styles/global.css'

interface Props {
  title?: string
  description?: string
}

const {
  title = 'Omar Alhajj — Software Engineer',
  description = 'Personal portfolio of Omar Alhajj, software engineer.',
} = Astro.props
---

<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <meta name="description" content={description} />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <title>{title}</title>

    <!-- Prevent flash of wrong theme -->
    <script is:inline>
      ;(function () {
        const stored = localStorage.getItem('theme')
        const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches
        if (stored === 'dark' || (!stored && prefersDark)) {
          document.documentElement.classList.add('dark')
        }
      })()
    </script>
  </head>
  <body>
    <slot />
  </body>
</html>
```

**Step 2: Verify build**

```bash
npm run build
```

**Step 3: Commit**

```bash
git add src/layouts/Layout.astro
git commit -m "feat: add Layout with theme flash prevention"
```

---

### Task 4: Create Header component

**Files:**
- Create: `src/components/Header.astro`

**Step 1: Write `src/components/Header.astro`**

```astro
---
const navLinks = [
  { label: 'About', href: '#about' },
  { label: 'Skills', href: '#skills' },
  { label: 'Experience', href: '#experience' },
  { label: 'Projects', href: '#projects' },
  { label: 'Contact', href: '#contact' },
]
---

<header class="sticky top-0 z-50 border-b" style="background-color: var(--color-bg); border-color: var(--color-border);">
  <div class="max-w-2xl mx-auto px-6 py-4 flex items-center justify-between">
    <a href="/" class="font-mono text-sm font-medium" style="color: var(--color-accent);">
      alhajj.dev
    </a>

    <nav class="flex items-center gap-6">
      {navLinks.map(link => (
        <a
          href={link.href}
          class="text-sm transition-colors hover:text-[var(--color-accent)]"
          style="color: var(--color-text-muted);"
        >
          {link.label}
        </a>
      ))}

      <button
        id="theme-toggle"
        aria-label="Toggle dark mode"
        class="ml-2 p-1 rounded transition-colors hover:text-[var(--color-accent)]"
        style="color: var(--color-text-muted);"
      >
        <!-- Sun icon (shown in dark mode) -->
        <svg id="icon-sun" xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="hidden">
          <circle cx="12" cy="12" r="4"/><path d="M12 2v2M12 20v2M4.93 4.93l1.41 1.41M17.66 17.66l1.41 1.41M2 12h2M20 12h2M6.34 17.66l-1.41 1.41M19.07 4.93l-1.41 1.41"/>
        </svg>
        <!-- Moon icon (shown in light mode) -->
        <svg id="icon-moon" xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
          <path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z"/>
        </svg>
      </button>
    </nav>
  </div>
</header>

<script>
  const toggle = document.getElementById('theme-toggle')
  const sun = document.getElementById('icon-sun')
  const moon = document.getElementById('icon-moon')
  const root = document.documentElement

  function updateIcons() {
    const isDark = root.classList.contains('dark')
    sun!.classList.toggle('hidden', !isDark)
    moon!.classList.toggle('hidden', isDark)
  }

  updateIcons()

  toggle!.addEventListener('click', () => {
    root.classList.toggle('dark')
    localStorage.setItem('theme', root.classList.contains('dark') ? 'dark' : 'light')
    updateIcons()
  })
</script>
```

**Step 2: Verify build**

```bash
npm run build
```

**Step 3: Commit**

```bash
git add src/components/Header.astro
git commit -m "feat: add sticky header with nav links and dark mode toggle"
```

---

### Task 5: Create Hero and About components

**Files:**
- Create: `src/components/Hero.astro`
- Create: `src/components/About.astro`

**Step 1: Write `src/components/Hero.astro`**

```astro
---
---

<section class="max-w-2xl mx-auto px-6 pt-24 pb-16 fade-in">
  <p class="font-mono text-sm mb-4" style="color: var(--color-accent);">Hi, I'm</p>
  <h1 class="text-4xl font-semibold tracking-tight mb-3" style="color: var(--color-text);">
    Omar Alhajj
  </h1>
  <p class="text-xl mb-6" style="color: var(--color-text-muted);">
    Software Engineer
  </p>
  <p class="text-base leading-relaxed" style="color: var(--color-text-muted);">
    I build things for the web — and occasionally beyond it.
  </p>
</section>
```

**Step 2: Write `src/components/About.astro`**

```astro
---
---

<section id="about" class="max-w-2xl mx-auto px-6 py-16 border-t fade-in" style="border-color: var(--color-border);">
  <h2 class="font-mono text-sm mb-6" style="color: var(--color-accent);">About</h2>
  <p class="text-base leading-relaxed mb-4" style="color: var(--color-text);">
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut
    labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco
    laboris nisi ut aliquip ex ea commodo consequat.
  </p>
  <p class="text-base leading-relaxed" style="color: var(--color-text);">
    Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla
    pariatur. Excepteur sint occaecat cupidatat non proident.
  </p>
</section>
```

**Step 3: Commit**

```bash
git add src/components/Hero.astro src/components/About.astro
git commit -m "feat: add Hero and About sections"
```

---

### Task 6: Create Skills component

**Files:**
- Create: `src/components/Skills.astro`

**Step 1: Write `src/components/Skills.astro`**

```astro
---
const skills = [
  'TypeScript', 'JavaScript', 'Python', 'Go',
  'React', 'Node.js', 'PostgreSQL', 'Docker',
  'AWS', 'Git', 'Linux', 'REST APIs',
]
---

<section id="skills" class="max-w-2xl mx-auto px-6 py-16 border-t fade-in" style="border-color: var(--color-border);">
  <h2 class="font-mono text-sm mb-6" style="color: var(--color-accent);">Skills</h2>
  <div class="flex flex-wrap gap-2">
    {skills.map(skill => (
      <span
        class="font-mono text-xs px-3 py-1.5 rounded border"
        style="color: var(--color-text-muted); border-color: var(--color-border); background-color: var(--color-card);"
      >
        {skill}
      </span>
    ))}
  </div>
</section>
```

**Step 2: Commit**

```bash
git add src/components/Skills.astro
git commit -m "feat: add Skills section with tag grid"
```

---

### Task 7: Create Experience component

**Files:**
- Create: `src/components/Experience.astro`

**Step 1: Write `src/components/Experience.astro`**

```astro
---
const experience = [
  {
    role: 'Senior Software Engineer',
    company: 'Company Name',
    period: '2023 — Present',
    description: 'Brief description of responsibilities and key achievements at this role.',
  },
  {
    role: 'Software Engineer',
    company: 'Another Company',
    period: '2021 — 2023',
    description: 'Brief description of responsibilities and key achievements at this role.',
  },
  {
    role: 'Junior Developer',
    company: 'First Company',
    period: '2019 — 2021',
    description: 'Brief description of responsibilities and key achievements at this role.',
  },
]
---

<section id="experience" class="max-w-2xl mx-auto px-6 py-16 border-t fade-in" style="border-color: var(--color-border);">
  <h2 class="font-mono text-sm mb-8" style="color: var(--color-accent);">Experience</h2>
  <div class="space-y-10">
    {experience.map(item => (
      <div class="grid grid-cols-[140px_1fr] gap-6">
        <p class="font-mono text-xs pt-1" style="color: var(--color-text-muted);">{item.period}</p>
        <div>
          <p class="font-medium text-sm mb-0.5" style="color: var(--color-text);">{item.role}</p>
          <p class="text-sm mb-2" style="color: var(--color-accent);">{item.company}</p>
          <p class="text-sm leading-relaxed" style="color: var(--color-text-muted);">{item.description}</p>
        </div>
      </div>
    ))}
  </div>
</section>
```

**Step 2: Commit**

```bash
git add src/components/Experience.astro
git commit -m "feat: add Experience section with timeline layout"
```

---

### Task 8: Create Projects component

**Files:**
- Create: `src/components/Projects.astro`

**Step 1: Write `src/components/Projects.astro`**

```astro
---
const projects = [
  {
    name: 'Project Alpha',
    description: 'A short description of what this project does and why it exists.',
    tech: ['TypeScript', 'React', 'PostgreSQL'],
    url: '#',
  },
  {
    name: 'Project Beta',
    description: 'A short description of what this project does and why it exists.',
    tech: ['Go', 'Docker', 'AWS'],
    url: '#',
  },
  {
    name: 'Project Gamma',
    description: 'A short description of what this project does and why it exists.',
    tech: ['Python', 'FastAPI', 'Redis'],
    url: '#',
  },
]
---

<section id="projects" class="max-w-2xl mx-auto px-6 py-16 border-t fade-in" style="border-color: var(--color-border);">
  <h2 class="font-mono text-sm mb-8" style="color: var(--color-accent);">Projects</h2>
  <div class="space-y-4">
    {projects.map(project => (
      <a
        href={project.url}
        target="_blank"
        rel="noopener noreferrer"
        class="block p-5 rounded-lg border transition-colors hover:border-[var(--color-accent)]"
        style="background-color: var(--color-card); border-color: var(--color-border);"
      >
        <p class="font-medium text-sm mb-1.5" style="color: var(--color-text);">{project.name}</p>
        <p class="text-sm leading-relaxed mb-3" style="color: var(--color-text-muted);">{project.description}</p>
        <div class="flex flex-wrap gap-1.5">
          {project.tech.map(tag => (
            <span class="font-mono text-xs px-2 py-0.5 rounded" style="color: var(--color-accent); background-color: var(--color-bg);">
              {tag}
            </span>
          ))}
        </div>
      </a>
    ))}
  </div>
</section>
```

**Step 2: Commit**

```bash
git add src/components/Projects.astro
git commit -m "feat: add Projects section with linked cards"
```

---

### Task 9: Create Contact and Footer components

**Files:**
- Create: `src/components/Contact.astro`
- Create: `src/components/Footer.astro`

**Step 1: Write `src/components/Contact.astro`**

```astro
---
const links = [
  { label: 'Email', href: 'mailto:omar@alhajj.dev', display: 'omar@alhajj.dev' },
  { label: 'GitHub', href: 'https://github.com/yourusername', display: 'github.com/yourusername' },
  { label: 'LinkedIn', href: 'https://linkedin.com/in/yourusername', display: 'linkedin.com/in/yourusername' },
]
---

<section id="contact" class="max-w-2xl mx-auto px-6 py-16 border-t fade-in" style="border-color: var(--color-border);">
  <h2 class="font-mono text-sm mb-6" style="color: var(--color-accent);">Contact</h2>
  <p class="text-base leading-relaxed mb-8" style="color: var(--color-text-muted);">
    I'm always open to interesting conversations, opportunities, or just a hello.
  </p>
  <div class="space-y-3">
    {links.map(link => (
      <div class="flex items-center gap-4">
        <span class="font-mono text-xs w-16" style="color: var(--color-text-muted);">{link.label}</span>
        <a
          href={link.href}
          target="_blank"
          rel="noopener noreferrer"
          class="text-sm transition-colors hover:text-[var(--color-accent)]"
          style="color: var(--color-text);"
        >
          {link.display}
        </a>
      </div>
    ))}
  </div>
</section>
```

**Step 2: Write `src/components/Footer.astro`**

```astro
---
const year = new Date().getFullYear()
---

<footer class="max-w-2xl mx-auto px-6 py-8 border-t" style="border-color: var(--color-border);">
  <p class="font-mono text-xs" style="color: var(--color-text-muted);">
    &copy; {year} Omar Alhajj
  </p>
</footer>
```

**Step 3: Commit**

```bash
git add src/components/Contact.astro src/components/Footer.astro
git commit -m "feat: add Contact and Footer sections"
```

---

### Task 10: Compose index.astro and add scroll animations

**Files:**
- Modify: `src/pages/index.astro`

**Step 1: Replace `src/pages/index.astro`**

```astro
---
import Layout from '../layouts/Layout.astro'
import Header from '../components/Header.astro'
import Hero from '../components/Hero.astro'
import About from '../components/About.astro'
import Skills from '../components/Skills.astro'
import Experience from '../components/Experience.astro'
import Projects from '../components/Projects.astro'
import Contact from '../components/Contact.astro'
import Footer from '../components/Footer.astro'
---

<Layout>
  <Header />
  <main>
    <Hero />
    <About />
    <Skills />
    <Experience />
    <Projects />
    <Contact />
  </main>
  <Footer />
</Layout>

<script>
  const observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add('visible')
          observer.unobserve(entry.target)
        }
      })
    },
    { threshold: 0.1 }
  )

  document.querySelectorAll('.fade-in').forEach((el) => observer.observe(el))
</script>
```

**Step 2: Verify build**

```bash
npm run build
```

Expected: clean build, no errors.

**Step 3: Preview locally**

```bash
npm run preview
```

Open `http://localhost:4321` and verify all sections render, dark mode toggle works, and scroll animations trigger.

**Step 4: Commit**

```bash
git add src/pages/index.astro
git commit -m "feat: compose index page and add scroll fade-in animations"
```

---

### Task 11: Add favicon and Netlify config

**Files:**
- Create: `public/favicon.svg`
- Create: `netlify.toml`

**Step 1: Create `public/favicon.svg`**

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32">
  <rect width="32" height="32" rx="6" fill="#6366f1"/>
  <text x="16" y="22" text-anchor="middle" font-family="monospace" font-size="16" font-weight="bold" fill="white">a</text>
</svg>
```

**Step 2: Create `netlify.toml`**

```toml
[build]
  command = "npm run build"
  publish = "dist"

[build.environment]
  NODE_VERSION = "20"
```

**Step 3: Final build verification**

```bash
npm run build
```

Expected: `dist/` contains `index.html` and all assets.

**Step 4: Commit**

```bash
git add public/favicon.svg netlify.toml
git commit -m "feat: add favicon and Netlify deploy config"
```

---

### Task 12: Write CLAUDE.md

**Files:**
- Create: `CLAUDE.md`

**Step 1: Write `CLAUDE.md`**

```markdown
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

- `npm run dev` — start dev server at `http://localhost:4321`
- `npm run build` — build static site to `dist/`
- `npm run preview` — preview the production build locally

## Architecture

Single-page Astro site. All content lives in `src/components/` as individual Astro components, assembled in `src/pages/index.astro`. `src/layouts/Layout.astro` wraps the page with `<html>`, global styles, and the theme flash-prevention script.

### Theming

Dark/light mode uses CSS custom properties defined in `src/styles/global.css` under `:root` and `:root.dark`. Tailwind is set to `darkMode: 'class'`. The `<html>` element gets a `dark` class toggled by `Header.astro`. An inline script in `Layout.astro` reads `localStorage` before paint to prevent flash.

### Adding content

- **Sections:** Create a new `.astro` component in `src/components/`, import and add it to `src/pages/index.astro`. Add a `fade-in` class to the section element.
- **New pages:** Add a `.astro` file to `src/pages/` — Astro handles routing automatically.
- **Updating placeholder data:** Each component (Skills, Experience, Projects, Contact) has a data array at the top of its frontmatter — edit those directly.

## Deployment

Netlify. Pushing to `main` triggers a deploy automatically once the site is connected in the Netlify dashboard. Config is in `netlify.toml`.
```

**Step 2: Commit**

```bash
git add CLAUDE.md
git commit -m "docs: add CLAUDE.md with architecture and development notes"
```

---

## Done

All tasks complete. The site is ready to connect to Netlify:

1. Push the repo to GitHub
2. In Netlify dashboard: **Add new site → Import an existing project → GitHub**
3. Build command: `npm run build`, publish directory: `dist`
4. Set custom domain to `alhajj.dev` in Netlify's domain settings
