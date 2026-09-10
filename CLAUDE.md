# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

"Five Suitcases & a Camera" — a static family travel journal. Three HTML pages plus one
shared stylesheet. No build step, no package manager, no dependencies, and **no JavaScript
anywhere** (not even inline handlers). Keep it that way unless the user explicitly asks
otherwise.

## Running it

```sh
python3 -m http.server 8000   # then http://localhost:8000
```

Opening the `.html` files directly with `file://` also works. There are no tests, no linter
and no build/CI configuration in the repo, so verification means loading the pages in a
browser and checking the markup by eye.

## Checking your work

There is nothing to run but a browser, so the duplicated chrome is where mistakes hide. Two
greps catch the common ones:

```sh
grep -c 'aria-current="page"' *.html   # must be exactly 1 per page
grep -c 'href="malaysia.html"' *.html  # nav + footer on every page (+ card/next-prev links)
```

Also confirm no JavaScript crept in — `grep -niE '<script|javascript:| on[a-z]+=' *.html`
should stay empty.

## Architecture

Flat, deliberately duplicated static site:

- `index.html` — home: hero, `.family` list of the five family members, `.cards` grid linking
  to each trip, `.gallery` photo box, closing `.article flush` section.
- `dubai.html`, `malaysia.html` — one trip per page, all following the same skeleton:
  `.hero.compact` → `figure.photo.trip-hero` → `dl.facts` (When / Length / Base-or-Route /
  Kid rating) → `article.article` with `<h2>` day headings, `figure.photo.tall` images,
  `blockquote` pull-quotes, and a closing `.next-prev` pair as the article's **last child**
  (not a sibling of it). A trip page may also drop a `.gallery.spaced` grid mid-article
  (see `malaysia.html`), so `.gallery` is not home-only.
- Every block of content sits inside a `<div class="wrap">` (max-width `--wrap`, 1040px,
  24px gutters). `index.html` opens a separate `.wrap` per band so the hero and the flush
  `.trip-hero` figure can breathe; the trip pages open one `.wrap` around the whole `<main>`.
  New top-level content needs a `.wrap` or it will run to the viewport edge.
- `style.css` — the single stylesheet for every page, organised in commented bands
  (header/nav, hero, sections, cards, photo placeholders, gallery, article, family list,
  footer).

**The header and footer are copy-pasted into every page.** There is no templating or include
mechanism, so any nav or footer change must be applied to all three files, and the current
page's nav link is marked with `aria-current="page"` (styled as the dark pill) — that
attribute must move, not be duplicated.

`README.md` repeats the file table, the conventions and the add-a-trip steps for humans;
when those change here, change them there too.

## Conventions to preserve

- **No inline styles.** Layout variants are modifier classes on the shared components:
  `.hero.compact`, `.photo.tall`, `.trip-hero.flush`, `.article.flush`, `.gallery.spaced`.
  Add a class in `style.css` rather than a `style=` attribute.
- **Two typefaces, by role.** `body` sets the serif stack (`"Iowan Old Style"` → Palatino →
  Georgia) for prose. Chrome and metadata — `.nav`, `.eyebrow`, `.section-note`, `.meta`,
  `.readmore`, `.photo figcaption`, `.facts dt`, `.site-footer`, `.next-prev` — each repeat
  the same `system-ui, -apple-system, "Segoe UI", sans-serif` stack literally (nine places;
  there is no `--font-*` custom property). Match that stack verbatim in new UI-ish rules.
- **Photos are CSS gradient placeholders**, not files: `.g-desert`, `.g-skyline`, `.g-souk`,
  `.g-marina`, `.g-jungle`, `.g-beach`, `.g-city`, `.g-tea`, `.g-family`, applied to
  `<figure class="photo …">`. Real photos go in as an `<img>` inside the figure with
  meaningful `alt` text; every `.photo` figure carries a `<figcaption>`. The one exception is
  the card thumbnail — `.card .thumb` is a bare decorative `<div>` with a gradient class, no
  figure and no caption.
- **Colours live as custom properties in `:root`** at the top of `style.css`. `--ink-faint`
  (5.4:1 on `--bg`) and `--accent` (5.2:1 on `--bg`) are tuned for WCAG AA on normal text —
  re-check contrast before changing either. Comments in the CSS record the measured ratios.
- **Accessibility and motion.** The card lift/transition sits inside
  `@media (prefers-reduced-motion: no-preference)`; keep motion opt-in. Caption gradients
  have a solid floor so a second caption line stays legible.
- **Markup shapes that look odd but are intentional.** `.card` is a single block-level `<a>`
  wrapping the thumb and body, so never nest another link or button inside one. `dl.facts`
  wraps each `dt`/`dd` pair in a `<div>` because the div is the grid cell — keep the wrapper.
- **Responsive layout is fluid, not breakpoint-driven.** `.cards`, `.gallery`, `.facts` and
  `.family` all use `repeat(auto-fit, minmax(…, 1fr))`. The only width media query is
  `max-width: 560px`, which shallows the sticky header once the nav wraps to a second row;
  prefer another `auto-fit` grid over adding a breakpoint.
- The favicon is an inline `data:image/svg+xml` camera in each page's `<head>`, sharing the
  `#9d5617` accent — it is duplicated per page like the header.
- **Light theme only.** `:root` declares `color-scheme: light` and there is no
  `prefers-color-scheme` block anywhere; the only media queries are `max-width: 560px` and
  `prefers-reduced-motion`. Don't introduce a dark mode unasked.
- **One `<h1>` per page**, inside the hero. `<h2>` is section titles on the home page and day
  headings inside a trip article; `<h3>` appears only in `.card-body`. Don't skip a level.
- **Prose voice and typography.** First-person plural family narration, British/European
  spelling (colour, favourite, jumper), metric units (`38°C`), curly quotes and apostrophes
  (`“…”`, `’`), en dashes in ranges (`Day 1–2`, `Days 8–12`) and `·` as the separator in
  `.eyebrow`, `.meta` and footer lines. `&` is written `&amp;` in `<title>` and `og:` values.

## Adding a trip page

1. Copy `dubai.html` and rewrite the article body.
2. Update `<title>`, `<meta name="description">` and the `og:` tags (trip pages use
   `og:type="article"`; `index.html` uses `website`).
3. Move `aria-current="page"` onto the new page's own nav link.
4. Add the link to the `<nav>` **and** the footer of *every* page.
5. Add a `.card` for it in the "Our trips" section of `index.html`, and fix up the
   `.next-prev` links on the neighbouring trip pages.
6. Reuse an existing `.g-*` gradient or add a new one to the photo-placeholder band in
   `style.css`; the `.eyebrow` on a trip hero carries the trip number (`Trip 03 · …`).
