# EPUB Cleaner

A web tool that **fully removes** promotional watermarks, ad pages, and tracking links from
EPUB files. Runs 100% in the browser — your book never leaves your device — and works
**offline** and installs as a **PWA** on phones and desktops.

This is an upgrade of the original EPUB Parser with three changes you asked for:

## What changed

### 1. Real removal, not just hidden text
The original did a plain text `replace()`, which deleted the literal text but left behind
the empty paragraph/heading/link that wrapped it (and ignored watermark *images* entirely)
— so the watermark was effectively invisible but still in the file. The new engine parses
each document as XML/XHTML and:

- **Deletes whole watermark-only elements** (the `<p>`, `<a>`, `<h1>`, `<div>`… that
  contained only the watermark) so no empty husks are left behind.
- **Removes watermark images** and any `<img>`/`<a>` that points at them, and drops the
  now-orphaned files from the archive.
- **Deletes inserted ad/watermark pages** outright, and prunes their entries from the
  `.opf` manifest/spine, the `.ncx` table of contents, and the nav document.
- **Cleans metadata** — e.g. a title like `1984 (some-site.com)` becomes `1984`.
- **Keeps real content**: a sentence that merely *mentions* the brand keeps its text
  (only the brand word is stripped); your cover image and prose are untouched.
- Rebuilds a **spec-valid EPUB** (`mimetype` stored first, uncompressed).

A non-removable case: if the watermark was painted directly onto the cover *image*,
that can't be undone automatically — the tool tells you when it saves.

### 2. Works offline
JSZip is now **bundled locally** (`jszip.min.js`) instead of loaded from a CDN, and a
**service worker** (`sw.js`) caches the whole app shell. After the first visit it runs
with no network at all.

### 3. Installable as a PWA
Added `manifest.webmanifest`, icons, and Apple meta tags, so you can install it to your
home screen and launch it like a native app.

## How to use
1. Open the site (or the installed app).
2. Drop one or more `.epub` files onto the box, or tap to choose them.
3. Each file gets its own result card with stats and a download link.
   - A single file downloads automatically (and via its card's button).
   - For multiple files, use each card's download link, or tap
     **Download all as ZIP** once everything finishes.

## Install on your phone
- **Android / Chrome / Edge:** open the site → menu → **Install app** (or tap the
  in-page *Install app* button).
- **iPhone / iPad (Safari):** open the site → **Share** → **Add to Home Screen**.

Once installed it works offline.

## Deploy
This repo is a GitHub Pages site. Replace the old `index.html` with the files in this
bundle (`index.html`, `jszip.min.js`, `sw.js`, `manifest.webmanifest`, and the icons),
commit, and push. It will be live at your Pages URL and installable. PWAs require HTTPS,
which GitHub Pages provides.

> When you ship an update, bump `CACHE` in `sw.js` (e.g. `epub-cleaner-v3`) so installed
> clients pick up the new version.

## Files
- `index.html` — the app (UI + removal engine, no external dependencies)
- `jszip.min.js` — vendored ZIP library (offline)
- `sw.js` — service worker (offline caching)
- `manifest.webmanifest` — PWA manifest
- `icon-192.png`, `icon-512.png`, `apple-touch-icon.png`, `favicon-*.png` — icons
