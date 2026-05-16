# docs/tutorial-format.md

# Tutorial Format Rules

A root-level file named:

`/TUTORIAL.md`

must always exist.

The file acts as:
- a build journal
- a Tailwind learning resource
- architecture documentation
- implementation notes

---

# Append Rules

After EVERY completed implementation step:

Append a new section.

Never overwrite previous sections unless explicitly asked.

---

# Required Format

```md
## Section Name

### Goal
What we built.

### Files changed
- file path
- file path

### Tailwind concepts used
Explain important utility classes briefly.

### Astro concepts used
Explain layouts, props, slots, islands, collections, etc.

### TypeScript concepts used
Explain interfaces/types briefly if relevant.

### Implementation notes
Important architecture or design notes.

### Code summary
Short plain-English summary.
```

---

# Tailwind Teaching Style

Assume:
- the user understands Astro reasonably well
- Tailwind knowledge is intermediate and improving

Explanations should be:
- concise
- practical
- implementation-focused

Avoid:
- overexplaining CSS basics
- giant Tailwind essays

---

# Good Explanation Examples

```md
- `mx-auto` centers the container horizontally
- `max-w-7xl` constrains line length and layout width
- `py-24` creates large vertical breathing room
- `border-black/10` creates a very subtle divider
- `md:grid-cols-2` switches to a 2-column layout on medium screens
```

---

# Tutorial Philosophy

The tutorial should become:
- readable months later
- useful for rebuilding the project
- educational without being bloated

Keep the tone:
- calm
- technical
- concise
- thoughtful