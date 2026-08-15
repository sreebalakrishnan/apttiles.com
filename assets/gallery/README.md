# Project gallery photographs

The homepage gallery (`index.html`, `#projects`) reads from the `shots` array at
the top of the page's `<script>`. Each entry names a file in this folder,
`assets/gallery/`. Nothing is fetched from Google Photos at runtime — see
"Why not live?" below.

## File naming

Every photograph exists at **two sizes, both WebP**:

- `<stem>-800.webp` — the grid tile
- `<stem>-1600.webp` — the lightbox

The `shots` array names only the **stem**, with no size and no extension, e.g.
`file: '01-courtyard-veranda'`. The page appends the suffix it needs.

## In the gallery

| Stem                        | Span   | Subject                                    |
| --------------------------- | ------ | ------------------------------------------ |
| `01-courtyard-veranda`      | `big`  | Courtyard arches, columns, ceiling tiles   |
| `02-brick-screen-wall`      | `tall` | Perforated hollow-brick wall with creeper  |
| `03-jali-room-dividers`     | `tall` | Framed jali screens, circle + leaf         |
| `04-vaulted-hall-ceiling`   | `big`  | Vaulted hall roofed in ceiling tiles       |
| `05-veranda-carved-door`    | `tall` | Veranda, carved door, brick columns        |
| `06-ceiling-tile-pattern`   | `tall` | Embossed ceiling tiles, close up           |
| `07-jali-blocks-detail`     | `wide` | Three jali patterns side by side           |
| `08-roofing-tiles-stacked`  | `tall` | Ridge and Mangalore tiles at the works     |
| `09-roof-structure-below`   | `tall` | Roof and truss seen from underneath        |
| `10-roof-underside-stamped` | `wide` | Tiles stamped A.P.T. TILES, BALAGHAT       |

## Spares

Uploaded but not currently shown. Swap one in by editing the `shots` array.

- `spare-solid-bricks-stacked` — solid bricks stacked on a garden chair
- `spare-solid-bricks-row` — four solid bricks in a row
- `spare-ridge-tile` — a single ridge tile on stacked tiles
- `spare-dimpled-floor-tile` — dimpled floor tile (feet in frame)

These exist at `-1600.webp` only. A spare needs an `-800.webp` made before it
can go in the grid.

## Adding a photograph

1. Export two WebP copies with the long edge at **800px** (quality ~76) and
   **1600px** (quality ~78). https://squoosh.app does this in the browser with
   no install; drag the photo in, pick WebP, set the size, export twice.
2. Name them `NN-short-description-800.webp` and `NN-short-description-1600.webp`
   and drop both in this folder.
3. Add an entry to the `shots` array in `index.html` using the stem alone.

Rough targets: grid copies land around 40–90 KB, lightbox copies 70–230 KB.

A file named in the array but missing from disk renders as a terracotta
pattern tile rather than a broken image, so the page survives a half-done
upload.

## Why WebP

The originals were 2.6 MB of phone JPEGs. At the sizes the page actually
displays them, WebP holds the same quality for roughly 60% less weight, and
splitting grid from lightbox means a visitor who never opens a photo never
downloads the large copy. The source JPEGs were removed once converted; they
remain in git history at commit `a5810d9` if a re-export is ever needed.

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
