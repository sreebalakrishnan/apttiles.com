# Project gallery photographs

The homepage gallery (`index.html`, `#projects`) reads from the `shots` array at
the top of the page's `<script>`. Each entry names a file in this folder,
`assets/gallery/`. Nothing is fetched from Google Photos at runtime — see
"Why not live?" below.

## In the gallery

| File                            | Span   | Subject                                    |
| ------------------------------- | ------ | ------------------------------------------ |
| `01-courtyard-veranda.jpg`      | `big`  | Courtyard arches, columns, ceiling tiles   |
| `02-brick-screen-wall.jpg`      | `tall` | Perforated hollow-brick wall with creeper  |
| `03-jali-room-dividers.jpg`     | `tall` | Framed jali screens, circle + leaf         |
| `04-vaulted-hall-ceiling.jpg`   | `big`  | Vaulted hall roofed in ceiling tiles       |
| `05-veranda-carved-door.jpg`    | `tall` | Veranda, carved door, brick columns        |
| `06-ceiling-tile-pattern.jpg`   | `tall` | Embossed ceiling tiles, close up           |
| `07-jali-blocks-detail.jpg`     | `wide` | Three jali patterns side by side           |
| `08-roofing-tiles-stacked.jpg`  | `tall` | Ridge and Mangalore tiles at the works     |
| `09-roof-structure-below.jpg`   | `tall` | Roof and truss seen from underneath        |
| `10-roof-underside-stamped.jpg` | `wide` | Tiles stamped A.P.T. TILES, BALAGHAT       |

## Spares

Uploaded but not currently shown. Swap one in by editing the `shots` array.

- `spare-solid-bricks-stacked.jpg` — solid bricks stacked on a garden chair
- `spare-solid-bricks-row.jpg` — four solid bricks in a row
- `spare-ridge-tile.jpg` — a single ridge tile on stacked tiles
- `spare-dimpled-floor-tile.jpg` — dimpled floor tile (feet in frame)

## Adding a photograph

1. Resize the long edge to **1600px**, save as JPEG at quality ~80, aiming for
   **under 300 KB**.
2. Name it `NN-short-description.jpg` and drop it in this folder.
3. Add an entry to the `shots` array in `index.html`.

A file named in the array but missing from disk renders as a terracotta
pattern tile rather than a broken image, so the page survives a half-done
upload.

## Spans

The mosaic is a 4-column grid:

| Value     | Size            | Suits                        |
| --------- | --------------- | ---------------------------- |
| `big`     | 2 cols × 2 rows | The strongest wide-scene shot |
| `tall`    | 1 col  × 2 rows | Portrait photographs         |
| `wide`    | 2 cols × 1 row  | Landscape photographs        |
| (omitted) | 1 col  × 1 row  | Small details                |

**Spans must total a multiple of 4** or the grid leaves holes. The current ten
come to 24 cells (2 `big` = 8, 2 `wide` = 4, 6 `tall` = 12), filling six rows
exactly. Note that a 1-row `wide` cannot sit beside a 2-row `tall` in the same
band — pair `wide` tiles so they stack in the same two columns.

An optional `pos` field sets `object-position` when a photo needs its framing
pushed away from an edge, e.g. `pos: 'bottom'` on `04`.

## Why not pull from Google Photos live?

Shared album links cannot be read by a static site:

- There is no public API for shared albums. The Library API's scopes were
  narrowed in 2025 to app-created media, and the Picker API needs an OAuth
  sign-in — neither works for anonymous visitors.
- Scraping the share page from the browser is blocked by CORS.
- The underlying `lh3.googleusercontent.com` URLs are signed and rotate, so
  hard-coding them breaks silently, weeks later.

Curating files into the repository keeps the gallery fast, gives us caption and
crop control, and survives any change on Google's side. The album link stays on
the page as the "see everything" overflow.
