# Project gallery photographs

The homepage gallery (`index.html`, `#projects`) reads from the `shots` array at
the top of the page's `<script>`. Each entry names a file that lives in this
folder. Nothing is fetched from Google Photos at runtime — see "Why not live?"
below.

## Adding a photograph

1. Export the photo from the Google Photos album at its original size.
2. Resize the long edge to **1600px** and save as JPEG, quality ~80.
   Aim for **under 300 KB** per file.
3. Save it here using the `NN-short-description.jpg` naming pattern.
4. Add an entry to the `shots` array in `index.html`.

## Expected files

| File                         | Span   | Subject                                     |
| ---------------------------- | ------ | ------------------------------------------- |
| `01-courtyard-veranda.jpg`   | `big`  | Courtyard arches, columns, patterned ceiling |
| `02-brick-screen-wall.jpg`   | `tall` | Perforated hollow-brick wall with creeper   |
| `03-jali-room-dividers.jpg`  | `tall` | Framed jali screens, circle + leaf patterns |
| `04-exhibition-stall.jpg`    | `wide` | A.P.T. Tiles trade exhibition stall         |
| `05-roofing-underside.jpg`   | `wide` | Roofing tiles on steel purlins, from below  |

Any file that is missing renders as a terracotta pattern tile rather than a
broken image, so the page stays presentable while photos are being collected.

## Spans

The mosaic is a 4-column grid. Spans control how each tile is placed:

| Value    | Size            | Suits                        |
| -------- | --------------- | ---------------------------- |
| `big`    | 2 cols × 2 rows | The strongest hero shot      |
| `tall`   | 1 col  × 2 rows | Portrait photographs         |
| `wide`   | 2 cols × 1 row  | Landscape photographs        |
| (omitted)| 1 col  × 1 row  | Details, filler              |

Spans should add up to a multiple of 4 so the grid has no holes. The current
five (`big` 4 + `tall` 2 + `tall` 2 + `wide` 2 + `wide` 2 = 12) fill three rows
exactly. A ten-photo set totalling 16, 20 or 24 will pack just as cleanly.

## Why not pull from Google Photos live?

Shared album links cannot be read by a static site:

- There is no public API for shared albums. The Library API's scopes were
  narrowed in 2025 to app-created media, and the Picker API needs an OAuth
  sign-in — neither works for anonymous visitors.
- Scraping the share page from the browser is blocked by CORS.
- The underlying `lh3.googleusercontent.com` URLs are signed and rotate, so
  hard-coding them breaks silently, weeks later.

Curating a handful of files into the repository keeps the gallery fast, gives
us caption and crop control, and survives any change on Google's side. The
album link stays on the page as the "see everything" overflow.
