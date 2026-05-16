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

### Code summary
Bare-bones studio homepage on Astro 4 + Tailwind v4. Editorial serif headline, restrained sans body, generous whitespace, light/dark tokens wired but no toggle yet.

### Config adjustments
Prettier config was updated after initial setup:
- `semi: false` — no semicolons
- `trailingComma: 'none'` — no trailing commas (note: use the string `'none'`, not `'false'` — `'false'` is not a valid Prettier value and will be silently ignored)

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
- The contact page mockup shows an optional far-right tagline in the header — that's a future prop, not wired yet.

### Code summary
Shared header component with responsive nav. Desktop shows inline links; mobile collapses to a hamburger that reveals a stacked nav. Active route is detected from `Astro.url.pathname` and visually indicated with a thin underline.
