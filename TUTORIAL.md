# floating.is — Build Tutorial

A build journal documenting how floating.is is constructed, section by section.

---

## Project Foundation

### Goal
Stand up the Astro + Tailwind v4 + TypeScript foundation with Vercel deployment, Prettier, ESLint, dark mode tokens, self-hosted fonts (Fraunces + Inter), and a minimal homepage placeholder.

### Files changed
- `package.json` (scripts: lint, format, check)
- `prettier.config.mjs`
- `.prettierignore`
- `eslint.config.mjs`
- `.vscode/extensions.json`
- `.vscode/settings.json`
- `src/styles/global.css` (design tokens via `@theme`)
- `src/layouts/BaseLayout.astro`
- `src/pages/index.astro`
- `astro.config.mjs` (Tailwind + Vercel via `astro add`)

### Tailwind concepts used
- `@theme { ... }` — Tailwind v4's CSS-first config. Every `--color-*` and `--font-*` token becomes a utility automatically (`bg-bg`, `text-ink`, `font-serif`).
- `@custom-variant dark` — defines the `dark:` variant against the `.dark` class on `<html>`.
- `mx-auto max-w-7xl px-6 md:px-8` — the studio's standard container pattern.
- `tracking-tight` / `tracking-wide` — letter-spacing for editorial headlines vs. uppercase labels.
- `leading-[1.05]` — arbitrary leading values via bracket syntax.

### Astro concepts used
- `BaseLayout.astro` + `<slot />` — single layout wraps every page.
- `is:inline` script — runs before hydration to set the dark-mode class without FOUC.
- `astro add` — official integration installer; idempotent.
- TypeScript `Props` interface inside the layout frontmatter — typed component props.

### TypeScript concepts used
- `interface Props` inside an `.astro` frontmatter is Astro's convention for typing `Astro.props`.

### Implementation notes
- Tailwind v4 uses `@tailwindcss/vite`, not the legacy `@astrojs/tailwind` integration.
- Dark mode is class-based (`.dark` on `<html>`), toggled later by an `AmbientToggle`. Inline pre-paint script reads `localStorage` + `prefers-color-scheme`.
- Fonts are self-hosted via `@fontsource/*` for speed + privacy.
- `@vercel/analytics/astro` only sends data in production, so dev sessions stay clean.

### Config adjustments
Prettier config was updated after initial setup:
- `semi: false` — no semicolons
- `trailingComma: 'none'` — no trailing commas (note: use the string `'none'`, not `'false'` — `'false'` is not a valid Prettier value and will be silently ignored)

### Code

**`prettier.config.mjs`**
```js
/** @type {import("prettier").Config} */
export default {
  printWidth: 100,
  singleQuote: true,
  semi: false,
  trailingComma: 'none',
  plugins: ['prettier-plugin-astro', 'prettier-plugin-tailwindcss'],
  overrides: [{ files: '*.astro', options: { parser: 'astro' } }],
}
```

**`.prettierignore`**
```
dist
.astro
.vercel
node_modules
TUTORIAL.md
```

**`eslint.config.mjs`**
```js
import js from '@eslint/js'
import tseslint from 'typescript-eslint'
import astro from 'eslint-plugin-astro'
import jsxA11y from 'eslint-plugin-jsx-a11y'
import globals from 'globals'

export default [
  { ignores: ['dist', '.astro', '.vercel', 'node_modules'] },
  js.configs.recommended,
  ...tseslint.configs.recommended,
  ...astro.configs.recommended,
  ...astro.configs['jsx-a11y-recommended'],
  {
    languageOptions: {
      globals: { ...globals.browser, ...globals.node },
    },
    rules: {
      '@typescript-eslint/no-unused-vars': ['error', { argsIgnorePattern: '^_' }],
    },
  },
]
```

**`.vscode/extensions.json`**
```json
{
  "recommendations": [
    "astro-build.astro-vscode",
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "unifiedjs.vscode-mdx"
  ]
}
```

**`.vscode/settings.json`**
```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": "explicit"
  },
  "[astro]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "tailwindCSS.experimental.classRegex": [
    ["class:list=\\{([^}]*)\\}", "[\"'`]([^\"'`]*)[\"'`]"]
  ],
  "files.associations": { "*.css": "tailwindcss" },
  "eslint.useFlatConfig": true,
  "eslint.validate": ["javascript", "typescript", "astro"]
}
```

**`src/styles/global.css`**
```css
@import 'tailwindcss';

@theme {
  --color-bg: #f6f3ef;
  --color-bg-2: #f4f1ed;
  --color-bg-3: #efebe7;
  --color-ink: #161616;
  --color-ink-soft: #1e1e1e;
  --color-muted: #7a746d;
  --color-muted-2: #8a847c;
  --color-accent: #97a8bb;

  --font-serif: 'Fraunces', ui-serif, Georgia, serif;
  --font-sans: 'Inter', ui-sans-serif, system-ui, sans-serif;
  --font-mono: 'Geist Mono Variable', ui-monospace, monospace;
}

@custom-variant dark (&:where(.dark, .dark *));

:root.dark {
  --color-bg: #06070a;
  --color-bg-2: #0b0d11;
  --color-bg-3: #0e1015;
  --color-ink: #f2eee8;
  --color-ink-soft: #f6f2ed;
  --color-muted: #8a847c;
  --color-accent: #97a8bb;
}

html {
  background: var(--color-bg);
  color: var(--color-ink);
  font-family: var(--font-sans);
  -webkit-font-smoothing: antialiased;
}
```

**`src/layouts/BaseLayout.astro`**
```astro
---
import '../styles/global.css'
import '@fontsource/fraunces/400.css'
import '@fontsource/fraunces/500.css'
import '@fontsource/inter/400.css'
import '@fontsource/inter/500.css'
import '@fontsource-variable/geist-mono'
import SiteHeader from '../components/SiteHeader.astro'
import SiteFooter from '../components/SiteFooter.astro'
import { Analytics } from '@vercel/analytics/astro'

interface Props {
  title?: string
  description?: string
}

const {
  title = 'floating.is',
  description = 'An independent software studio.',
} = Astro.props
---

<!doctype html>
<html lang="en" class="">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="description" content={description} />
    <title>{title}</title>
    <script is:inline>
      const stored = localStorage.getItem('theme')
      const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches
      if (stored === 'dark' || (!stored && prefersDark)) {
        document.documentElement.classList.add('dark')
      }
    </script>
  </head>
  <body class="min-h-dvh bg-bg text-ink antialiased">
    <SiteHeader />
    <slot />
    <SiteFooter />
    <Analytics />
  </body>
</html>
```

**`src/pages/index.astro`** (placeholder, replaced in homepage hero step)
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro'
---

<BaseLayout>
  <main class="mx-auto max-w-7xl px-6 py-32 md:px-8 md:py-48">
    <p class="font-sans text-sm tracking-wide text-muted uppercase">floating.is</p>
    <h1 class="mt-8 font-serif text-6xl leading-[1.05] tracking-tight text-ink md:text-8xl">
      Quiet software,<br />made slowly.
    </h1>
    <p class="mt-10 max-w-xl font-sans text-base leading-relaxed text-muted md:text-lg">
      An independent studio building calm, considered tools.
    </p>
  </main>
</BaseLayout>
```

### Code summary
Bare-bones studio homepage on Astro + Tailwind v4. Editorial serif headline, restrained sans body, generous whitespace, light/dark tokens wired but no toggle yet.

---

## SiteHeader

### Goal
Build the shared site header: wordmark left, nav right on desktop, hamburger toggle on mobile with a slide-down nav.

### Files changed
- `src/components/SiteHeader.astro` (new)
- `src/layouts/BaseLayout.astro` (added SiteHeader import + usage)

### Tailwind concepts used
- `hidden md:flex` — hides on mobile, switches to flex at the `md` breakpoint (768px+).
- `justify-between` — pushes wordmark and nav to opposite ends of the flex row.
- `items-center` — vertically centers all flex children.
- `underline underline-offset-4 decoration-1` — thin underline with spacing, used for the active nav item.
- `gap-[5px]` — arbitrary value syntax for the hamburger line spacing.
- `transition-opacity hover:opacity-60` — subtle fade on nav link hover.
- `class:list={[...]}` — Astro utility for conditionally joining class strings.

### Astro concepts used
- `Astro.url.pathname` — available in any `.astro` component (pages and layouts alike), used to detect the active route without props.
- `<script>` in a component — Astro bundles and deduplicates these automatically; used here for the hamburger toggle.
- Component import in `BaseLayout.astro` — dropping `<SiteHeader />` in the layout means every page gets it automatically.

### TypeScript concepts used
- Inline helper function `isActive(href: string): boolean` in the frontmatter — keeps the JSX clean.

### Implementation notes
- The mobile nav (`#mobile-nav`) uses `hidden`/`flex` class-toggling via a small vanilla script — no framework needed.
- `aria-expanded` and `aria-hidden` are toggled in sync so screen readers track the open/closed state correctly.
- Active link gets `!text-ink` (the `!` prefix forces the value over the default `text-muted`) plus a thin underline.
- Nav links use Geist Mono (font-mono) at font-light weight to match the mockup's geometric, understated style.
- The contact page mockup shows an optional far-right tagline in the header — that's a future prop, not wired yet.

### Code

**`src/components/SiteHeader.astro`**
```astro
---
const navLinks = [
  { href: '/work', label: 'Work' },
  { href: '/studio', label: 'Studio' },
  { href: '/journal', label: 'Journal' },
  { href: '/contact', label: 'Contact' },
]

const { pathname } = Astro.url

function isActive(href: string) {
  return pathname === href || pathname.startsWith(href + '/')
}
---

<header class="flex items-center justify-between px-6 py-6 md:px-8 md:py-8">
  <a href="/" class="font-sans text-base font-medium text-ink">
    floating.is
  </a>

  <nav class="hidden items-center gap-8 md:flex" aria-label="Primary">
    {navLinks.map(({ href, label }) => (
      <a
        href={href}
        class:list={[
          'font-mono font-light text-sm text-muted transition-opacity hover:opacity-60',
          isActive(href) && 'decoration-1 underline underline-offset-4 !text-ink'
        ]}
      >
        {label}
      </a>
    ))}
  </nav>

  <button
    id="menu-toggle"
    class="flex flex-col gap-[5px] md:hidden"
    aria-label="Open menu"
    aria-expanded="false"
  >
    <span class="block h-px w-6 bg-ink"></span>
    <span class="block h-px w-6 bg-ink"></span>
    <span class="block h-px w-6 bg-ink"></span>
  </button>
</header>

<div id="mobile-nav" class="hidden flex-col gap-6 px-6 pb-8" aria-hidden="true">
  {navLinks.map(({ href, label }) => (
    <a
      href={href}
      class:list={[
        'font-mono font-light text-2xl text-muted',
        isActive(href) && 'decoration-1 underline underline-offset-4 !text-ink'
      ]}
    >
      {label}
    </a>
  ))}
</div>

<script>
  const toggle = document.getElementById('menu-toggle')
  const nav = document.getElementById('mobile-nav')

  toggle?.addEventListener('click', () => {
    const isOpen = nav?.classList.contains('flex')
    nav?.classList.toggle('hidden', isOpen)
    nav?.classList.toggle('flex', !isOpen)
    toggle.setAttribute('aria-expanded', String(!isOpen))
    nav?.setAttribute('aria-hidden', String(isOpen))
  })
</script>
```

### Code summary
Shared header component with responsive nav. Desktop shows inline links in Geist Mono; mobile collapses to a hamburger that reveals a stacked nav. Active route is detected from `Astro.url.pathname` and visually indicated with a thin underline.

---

## SiteFooter

### Goal
Build the shared site footer with a 4-column layout (wordmark, Explore links, Connect, location) and the ambient dark/light theme toggle button.

### Files changed
- `src/components/SiteFooter.astro` (new)
- `src/layouts/BaseLayout.astro` (added SiteFooter import + usage)

### Tailwind concepts used
- `grid grid-cols-2 md:grid-cols-4` — 2-column on mobile, 4-column on desktop.
- `col-span-2 md:col-span-1` — wordmark spans full width on mobile, single column on desktop.
- `text-[10px] uppercase tracking-widest` — column header style using an arbitrary font size below Tailwind's `text-xs`.
- `border-t border-black/5 dark:border-white/10` — subtle top divider with dark mode variant.
- `absolute bottom-6 right-6` — pins the ambient toggle to the footer's bottom-right corner; requires `relative` on the footer.
- `space-y-2` — vertical rhythm between list items without a wrapper element.

### Astro concepts used
- `new Date().getFullYear()` in frontmatter — dynamic copyright year.
- `<script>` for the toggle — vanilla JS, no framework; Astro bundles and deduplicates it.

### Implementation notes
- The theme toggle writes `'dark'` or `'light'` to `localStorage`. The `is:inline` script in `BaseLayout` reads this on every page load to avoid FOUC.
- The footer is `position: relative` so the absolutely-positioned toggle stays within the footer bounds rather than the viewport.
- Copyright year is computed at build time via `new Date().getFullYear()`.

### Code

**`src/components/SiteFooter.astro`**
```astro
---
const year = new Date().getFullYear()

const exploreLinks = [
  { href: '/work', label: 'Work' },
  { href: '/studio', label: 'Studio' },
  { href: '/journal', label: 'Journal' },
]
---

<footer class="relative mt-24 border-t border-black/5 dark:border-white/10">
  <div class="mx-auto max-w-7xl px-6 py-12 md:px-8 md:py-16">
    <div class="grid grid-cols-2 gap-10 md:grid-cols-4">

      <div class="col-span-2 md:col-span-1">
        <a href="/" class="font-sans text-sm font-medium text-ink">floating.is</a>
        <p class="mt-2 max-w-[160px] font-sans text-xs leading-relaxed text-muted">
          One person studio making quiet software.
        </p>
      </div>

      <div>
        <p class="font-sans text-[10px] uppercase tracking-widest text-muted">Explore</p>
        <ul class="mt-4 space-y-2">
          {exploreLinks.map(({ href, label }) => (
            <li>
              <a href={href} class="font-sans text-xs text-ink transition-opacity hover:opacity-60">
                {label}
              </a>
            </li>
          ))}
        </ul>
      </div>

      <div>
        <p class="font-sans text-[10px] uppercase tracking-widest text-muted">Connect</p>
        <ul class="mt-4 space-y-2">
          <li>
            <a
              href="mailto:hello@floating.is"
              class="font-sans text-xs text-ink transition-opacity hover:opacity-60"
            >
              hello@floating.is
            </a>
          </li>
        </ul>
      </div>

      <div>
        <p class="font-sans text-[10px] uppercase tracking-widest text-muted">Austin, Texas</p>
        <p class="mt-4 font-sans text-xs text-muted">© {year} floating.is</p>
        <p class="font-sans text-xs text-muted">All rights reserved.</p>
      </div>

    </div>
  </div>

  <button
    id="theme-toggle"
    aria-label="Toggle dark mode"
    class="absolute bottom-6 right-6 flex h-8 w-8 items-center justify-center rounded-full border border-black/20 transition-opacity hover:opacity-60 dark:border-white/20 md:bottom-8 md:right-8"
  >
    <span class="h-1.5 w-1.5 rounded-full bg-ink"></span>
  </button>
</footer>

<script>
  const toggle = document.getElementById('theme-toggle')
  toggle?.addEventListener('click', () => {
    const isDark = document.documentElement.classList.toggle('dark')
    localStorage.setItem('theme', isDark ? 'dark' : 'light')
  })
</script>
```

### Code summary
Shared footer with 4-column grid layout. Wordmark and tagline left, navigation links, email, and location right. Ambient circle toggle bottom-right wires up dark/light mode — clicking it toggles the `.dark` class on `<html>` and persists the choice to localStorage.
