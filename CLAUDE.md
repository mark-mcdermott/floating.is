# CLAUDE.md

You are helping build `floating.is`, an editorial-style software studio website.

Stack:
- Astro
- Tailwind CSS
- TypeScript
- Vercel
- Vercel Analytics

Preferred patterns:
- server-first rendering
- minimal hydration
- reusable components
- calm, maintainable architecture

## Reference Docs

Additional project documentation lives in `/docs`.

Use these files as canonical references while building:

- `docs/design-system.md`
  - visual language
  - spacing philosophy
  - typography
  - color palette
  - overall aesthetic direction

- `docs/site-map.md`
  - overall site architecture
  - page purposes
  - page hierarchy
  - likely sections/components

- `docs/tutorial-format.md`
  - required format for `/TUTORIAL.md`
  - tutorial writing expectations
  - Tailwind teaching style

Follow these documents closely and keep implementations visually consistent with them.

We are building the site incrementally, section-by-section and page-by-page.

## Workflow

For every task:

1. Explain what we are building
2. Explain why it exists
3. List files being changed
4. Briefly explain important Tailwind utilities
5. Implement the code
6. Append notes to `/TUTORIAL.md`

Do not skip tutorial updates.
Do not massively refactor unrelated files unless explicitly asked.

---

# Tutorial File

Maintain:

`/TUTORIAL.md`

Append after every completed step:

```md
## Section Name

### Goal

### Files changed

### Tailwind concepts used

### Astro concepts used

### Implementation notes

### Code summary
```

Never overwrite previous tutorial sections unless asked.

---

# Design Direction

The site should feel:
- quiet
- premium
- editorial
- spacious
- restrained
- architectural
- atmospheric

Avoid:
- loud SaaS styling
- bright gradients
- generic startup UI
- glassmorphism
- oversized shadows
- cluttered layouts

Whitespace is critical.

---

# Typography

Use:
- editorial serif for major headings
- restrained sans-serif for UI/body

Headings should feel:
- elegant
- oversized
- spacious
- calm

---

# Tailwind Guidance

Assume the user understands Astro reasonably well but wants help improving Tailwind fluency.

Briefly explain utilities like:
- spacing
- layout
- borders
- responsive modifiers
- typography classes

Keep explanations concise.

---

# Code Style

- TypeScript preferred
- Prefer `.astro` components
- Minimal client-side JavaScript
- Semantic HTML
- Accessible markup
- Small reusable components
- No unnecessary abstraction
- Avoid unnecessary dependencies
- Prefer CSS variables for reusable theme values

---

# Architecture

Prefer reusable components like:
- `SiteHeader`
- `SiteFooter`
- `Container`
- `AmbientToggle`
- `ProjectCard`
- `JournalCard`

Keep components readable and production-quality.

---

# Before Building

Before implementing:
- explain the plan
- explain the layout strategy
- list changed files

Then code.

---

# After Building

After implementation:
- summarize what was built
- suggest verification steps
- suggest:
  - `npm run check`
  - `npm run build`
- append notes to `/TUTORIAL.md`