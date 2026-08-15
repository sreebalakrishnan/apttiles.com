# apttiles.com

Marketing site for **APT Tiles** (A.P.T. Tiles, brand त्रिशूल छाप / Trishul), an
architectural terracotta manufacturer at Garden Road, Garra, Balaghat, Madhya
Pradesh. Enquiries run over WhatsApp on **+91 99773 01100**; there is no email
or CRM.

## Shape of the project

Two hand-written static pages plus an assets folder. **No build step, no
dependencies, no package.json, no tests.** CSS and JS are inline in each page.
Edit the HTML directly.

```
index.html        landing page — hero, products, gallery, material, process,
                  design + video, story, FAQ, contact
products.html     catalogue — 27 products, filter, search, detail modal
assets/           images, fonts, icons (117 files, ~5.8 MB)
  gallery/        project photographs + README.md documenting the workflow
  products/       catalogue images
.htaccess         MIME, compression, caching, directory protection
robots.txt  sitemap.xml  llms.txt
```

## Deployment — merging is deploying

A **Hostinger Git integration pulls `main` automatically**. There is no CI, no
Pages workflow, no staging. Anything merged to `main` is live within minutes.

Work on the branch named in the session instructions, push, open a PR, and
**wait to be asked before merging** — the user merges deliberately, and a merge
is a production deploy.

## Conventions that must not be undone

These each cost real work to establish. Check here before "simplifying" any of
them.

### Never inline images as base64

`index.html` was 822 KB and `products.html` 3.6 MB because every image was a
data URI. That blocks first paint entirely — nothing renders, and no other
image is even discovered, until the whole document arrives. They are now 90 KB
and 58 KB. **Images are always separate files.**

### Image pipeline

Everything is WebP except the social image, which is JPEG because WhatsApp
handles WebP previews inconsistently.

| Purpose | Naming | Size / quality |
| --- | --- | --- |
| Gallery grid tile | `<stem>-800.webp` | 800px long edge, q0.76 |
| Gallery lightbox | `<stem>-1600.webp` | 1600px long edge, q0.78 |
| Catalogue card | `<id>-card.webp` | 600px, q0.76 |
| Catalogue detail | `<id>-app.webp` | 1200px, q0.78 |
| Technical drawing | `<id>-tech.webp` | 900px, q0.80 |
| Hero | `hero-courtyard.webp` | q0.72, ~128 KB (LCP, preloaded) |
| Social card | `og-image.jpg` | 1200×630 JPEG |

There is no image tooling installed. Conversions were done by driving headless
Chromium's canvas — see "Verifying work" below.

### Bilingual text: `data-en` / `data-hi`, and the pipe

Every translatable node carries `data-en` and `data-hi`. `setLang()` rebuilds
the text as DOM nodes via `setText()`.

**A pipe `|` in the attribute means a line break.** This exists because the
original code assigned `textContent`, which destroyed the `<br>` in the markup —
six headings collapsed to one line on the first toggle and never recovered.
**Never reintroduce `textContent` assignment for translatable nodes.**

The hero's `#mitti` element is deliberately *not* a translation: it shows
मिट्टी in English mode and Terracotta in Hindi mode, so the counterpoint word is
always in the other script. Its `lang` attribute flips with it.

### Devanagari typography

Georgia has no Devanagari glyphs. `assets/noto-serif-devanagari-subset.woff2`
(28 KB) is subset to the 57 characters the two pages use, with layout features
kept so the ट्ट conjunct in मिट्टी forms. If Hindi text is added that uses new
characters, **the subset must be regenerated** or those characters fall back to
a system font.

`html[lang="hi"]` relaxes `letter-spacing` to normal and `line-height` to 1.32.
Latin tracking crushes the शिरोरेखा and the matras above and below it.

### Content is pre-rendered, then JS overwrites it identically

The 27 product cards and 10 gallery tiles are **written into the HTML**, and the
scripts replace them with identical markup on load. This is for crawlers: when
the content was built only at runtime, `products.html` offered 475 characters
and zero products to anything that does not execute JavaScript.

**If you change the card or tile template in JS, change the pre-rendered markup
to match.** They must stay identical or the page flickers on load.

### Structured data

`index.html` — `LocalBusiness`, `WebSite`, `VideoObject`, `FAQPage`.
`products.html` — `BreadcrumbList`, `CollectionPage`, `ItemList` of all 27.

The FAQ's visible section and its schema are generated from one source so they
cannot drift.

### Do not invent business facts

The following are **not published anywhere** and must never be asserted in
copy, schema or `llms.txt`: price, minimum order quantity, lead time,
certifications, founding date, geo coordinates, email address. Ask instead.
Wrong facts in machine-readable form propagate into answer engines.

## Platform rules worth remembering

- **A cross-origin iframe does not inherit the click that created it**, and
  `postMessage` does not carry user activation. On iOS/iPadOS Safari,
  sound-on autoplay in a third-party iframe is impossible by any construction.
  The video therefore starts muted (`START_MUTED` in `index.html`) — the only
  thing browsers permit unconditionally.
- **Never `display:none` a `loading="lazy"` image.** The browser will not load
  an image it is not rendering, so the `load` event never fires. The video
  poster deadlocked on exactly this.
- `.htaccess` — every directive is `<IfModule>`-guarded. **`Options -Indexes`
  is the one line that can 500** the site, because the guard covers a missing
  module but not the host disallowing `Options`. Delete it first if the site
  errors after a deploy. No HTTPS redirect: Hostinger terminates TLS upstream,
  so `%{HTTPS}` is unreliable and would loop. Use hPanel's Force HTTPS toggle.

## Verifying work

There is no test suite. Verify by driving headless Chromium:

```bash
cd <scratchpad> && npm i playwright-core
# browser at /opt/pw-browsers/chromium-1194/chrome-linux/chrome, needs --no-sandbox
```

Useful checks: render at 1400px and 390px, toggle EN→HI→EN and confirm line
breaks survive, open the lightbox and page with arrow keys, load with
`javaScriptEnabled:false` to see what a crawler sees, and watch `pageerror`.

Two traps:

- Over `file://`, the font preload logs a **CORS error that does not occur over
  HTTPS**. Serve with `python3 -m http.server` before believing it.
- `response.body()` silently fails for `file://` images, so byte counters
  **undercount badly**. Resolve requests to files on disk and stat them. An
  early "80 KB first view" figure was wrong for this reason; the real number
  was 319 KB.

## Known issues

- Gallery photos 04 and 05 carry a misspelled **"Celing Tiles"** sticker. It is
  cropped out of the grid tiles but still visible in the lightbox, which shows
  full frames. Needs clean originals from the client.
- The **27 catalogue images are CGI renders** with the printed catalogue's page
  numbers visible in a corner. Replace when real product photography exists.
- All 27 products live at `products.html#<id>`. Search engines cannot rank a
  fragment; **individual product pages** are the biggest remaining SEO lever.
- Hindi content lives in `data-hi` attributes, so it is **not indexable**. The
  site is effectively English-only for search.
- The two pages have slightly diverged CSS tokens (`--sand`, `--cream`).
- Hindi FAQ answers are machine translations awaiting a native review.

## Getting files from the client

Photographs have been the recurring friction. Pasting into chat is unreliable —
attachments sometimes arrive with no file on disk. **Ask for a shared Google
Drive folder** and read it with the Drive tools; a broad Drive search reaches
personal documents, so always scope to a named folder. Ask for **originals at
full resolution**, never WhatsApp-forwarded copies, which are heavily
recompressed.
