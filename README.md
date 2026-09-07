# Five Suitcases & a Camera

A small static website holding the Rodríguez family's holiday write-ups —
travel stories, trip facts and photo placeholders. No build step, no
dependencies, no JavaScript: three HTML pages and one shared stylesheet.

## Files

| File            | What it is                                            |
| --------------- | ----------------------------------------------------- |
| `index.html`    | Home — intro, family list, trip cards, photo box       |
| `dubai.html`    | Trip 01 — Dubai, March 2025                            |
| `malaysia.html` | Trip 02 — Malaysia, August 2025                        |
| `style.css`     | Shared stylesheet for all pages                        |

## Viewing it locally

Open any `.html` file directly in a browser, or serve the folder:

```sh
python3 -m http.server 8000   # then visit http://localhost:8000
```

## Adding a trip

1. Copy `dubai.html` as a starting point and rewrite the article body.
2. Update the `<title>`, `<meta name="description">` and the `og:` tags in `<head>`.
3. Move `aria-current="page"` onto the new page's own nav link.
4. Add the new link to the `<nav>` **and** the footer of *every* page —
   the header and footer are duplicated per page, so all of them need editing.
5. Add a card for the trip in the "Our trips" section of `index.html`.

## Conventions

- **Colours** live as custom properties in `:root` at the top of `style.css`.
  `--ink-faint` and `--accent` are set to meet WCAG AA (4.5:1) against the
  page background; check contrast before changing either.
- **Photos** are currently CSS gradient placeholders (`.g-desert`, `.g-tea`, …)
  inside `<figure class="photo">`. Real photos go in as an `<img>` inside the
  figure, each with meaningful `alt` text.
- **No inline styles.** Variants are classes: `.photo.tall`, `.hero.compact`,
  `.trip-hero.flush`, `.article.flush`, `.gallery.spaced`.
