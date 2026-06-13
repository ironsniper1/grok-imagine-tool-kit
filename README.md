# Grok Imagine Toolkit

A single [Tampermonkey](https://www.tampermonkey.net/) userscript that adds three independent power tools to [Grok Imagine](https://grok.com/imagine):

1. **Favorites Search** — full-text prompt search across all your saved images, backed by a local index.
2. **Tag Manager** — organize your saved images into Grok's native tags (folders) from a searchable grid, with per-image and bulk tagging.
3. **Bulk Favorites Downloader** — download your entire favorites library (or a single tag) in batches, with download history so you never grab the same file twice.

All three run side by side without fighting over screen space: the Tag Manager and Downloader share one tidy bottom-right dock, and the search bar floats top-center clear of Grok's own toolbar.

---

## Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
  - [Recommended first run](#recommended-first-run)
  - [Favorites Search](#favorites-search)
  - [Tag Manager](#tag-manager)
  - [Bulk Favorites Downloader](#bulk-favorites-downloader)
- [How it works](#how-it-works)
- [Performance](#performance)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Notes & caveats](#notes--caveats)
- [License](#license)

---

## Features

### Favorites Search
- Floating search bar on the **Saved** page (`/imagine/saved`).
- Builds a local [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) index (`GrokSearchIndex`) of every saved image and its prompt.
- **Incremental updates** — after the first full index, later visits only fetch images newer than what's already stored, stopping as soon as it hits a known one.
- Multi-term search (all terms must match), newest/oldest sort, and a paginated results grid.
- Result cards are real links, so middle-click, Ctrl/Cmd-click, and "copy link" all work.
- Keyboard shortcuts: `Ctrl/Cmd+F` to focus, `Esc` to blur, `←` / `→` to page through results.

### Tag Manager
- Launches from a **🏷 Tags** button into a full-screen, searchable grid.
- Reads the same `GrokSearchIndex` the search tool builds (read-only) and writes tags straight to Grok through its native folder API.
- Create tags, add a single image to a tag via a per-card popover, or **bulk-select** and tag many at once.
- Sidebar filters: **All**, **Untagged**, and one entry per tag with live counts.
- Tag membership counts load when you first open the panel — not on page load — so it stays out of your way until you use it.

### Bulk Favorites Downloader
- Bottom-right button stack: **⬇ Download Favorites**, **🏷 Download by Tag**, plus a status log and a reset link.
- Downloads in chunks (200 files per batch with a short pause between batches) to avoid hammering the browser.
- **Remembers what it downloaded** in script storage and skips those on the next run.
- Action modal options:

  | Option | What it does |
  | --- | --- |
  | Download new favorites | Fetch everything, skip already-downloaded, save the rest |
  | Save IDs only | Mark items as "already have" without downloading (full scan) |
  | Quick sync IDs | Same, but only scans the newest *N* pages (fast, for multi-device use) |
  | Download everything | Re-download all favorites, ignoring history |

- Media-type filter (images, videos, or both) and an optional max-pages limit.
- **Download by Tag** downloads only the items inside a chosen tag, with an optional "skip already downloaded" toggle.
- A per-card **⬇** button appears on hover over any image, downloading that post (and its variations) directly — it identifies posts via React fiber props, so it works even when the thumbnail is a base64 blob with no URL.
- Files land in `Downloads/grok-favorites/`, named `{id}_{model}_{prompt}.{ext}`.

---

## Requirements

- A browser with a userscript manager. **Tampermonkey** is recommended — the script relies on `GM_download`, `GM_xmlhttpRequest`, `GM_setValue`, and `GM_getValue`.
- An active, logged-in [grok.com](https://grok.com) session (the script uses your existing cookies; it never asks for credentials).
- Some saved/favorited images in Grok Imagine to work with.

---

## Installation

1. Install the [Tampermonkey](https://www.tampermonkey.net/) extension for your browser.
2. Open `Grok-Imagine-Toolkit.user.js` (e.g. the **raw** file link in this repo). Tampermonkey will detect it and show an install page.
3. Click **Install**.
4. Open [grok.com/imagine](https://grok.com/imagine) and the tools appear automatically.

> **Upgrading from the three separate scripts?** Uninstall the old *Grok Imagine Favorites Search*, *Grok Imagine Tag Manager*, and *Grok Imagine Bulk Favorites Downloader* scripts first, or you'll get duplicate buttons and two copies of the indexer running at once.

---

## Usage

### Recommended first run

The Tag Manager reads the index that the search tool builds, so do this once in order:

1. Go to the **Saved** page (`/imagine/saved`).
2. Let the search bar finish its first-time indexing (the status text in the bar shows progress). Large libraries can take a little while.
3. From then on, both Search and Tag Manager have a full local index to work with, and future visits only top it up incrementally.

### Favorites Search
Start typing in the top-center bar to filter your saved images by prompt. Use the sort dropdown to flip newest/oldest, the arrows (or `←` / `→`) to page, and the **✕** to clear.

### Tag Manager
Click **🏷 Tags** (bottom-right) to open the grid. Search prompts at the top, pick a filter on the left, and:
- Click the **🏷** on any card to add it to a tag (or create a new one inline).
- Click **Select**, tick multiple images, choose a tag, and **Add to tag** to bulk-tag them.
- Use **↻** to reload tags and counts from Grok.

### Bulk Favorites Downloader
Click **⬇ Download Favorites** and choose an action from the modal, or **🏷 Download by Tag** to grab a single tag. Hover any image and click its **⬇** to download just that one. Use **🗑 Reset download history** if you want a future run to re-fetch everything.

---

## How it works

The script is one outer wrapper containing three self-contained modules, each keeping its original logic intact. Two small pieces are shared:

- **`#grok-toolkit-dock`** — a fixed bottom-right column that the Tag Manager button and the Downloader stack are placed into, so they stack neatly instead of overlapping each other or Grok's UI.
- **A single-page-app path watcher** — Grok changes the URL without reloading, so the toolkit watches for navigation and shows/hides the right tools per page (the search bar only on Saved, the Tag Manager only on Imagine pages).

The **Favorites Search** module owns the `GrokSearchIndex` IndexedDB store; the **Tag Manager** reads from it. The **Downloader** keeps its own download history in script storage (`grokdl_downloaded_ids`). The modules don't otherwise share state, and each uses its own ID/CSS prefix (`grok-*`, `gtm-*`, `grokdl-*`) to avoid collisions.

---

## Performance

A couple of deliberate choices keep the toolkit light:

- **Deferred tag scan.** The Tag Manager no longer pages through every tag's full membership on page load. It fetches just the tag *list* up front (cheap) and only does the heavy membership scan the first time you actually open the panel. If you never open it, it does almost nothing in the background.
- **Throttled card watcher.** The Downloader's per-card buttons are added by a DOM observer. Because Grok's virtual scroll churns the DOM constantly, the observer coalesces bursts of changes into at most one pass every 200 ms instead of one per change.

If the page still feels heavy on a very large library, the next lever is scoping the card observer to the grid container rather than the whole page.

---

## Configuration

Each module has tunable constants near the top of its section in the script:

| Constant | Module | Default | Meaning |
| --- | --- | --- | --- |
| `FAVORITES_PATH` | Search | `/imagine/saved` | Where the search bar appears |
| `PAGE_SIZE` | Search | `20` | Results per page |
| `CHUNK_SIZE` | Downloader | `200` | Files per download batch |
| `CHUNK_PAUSE_MS` | Downloader | `5000` | Pause between batches (ms) |
| `API_DELAY_MS` | Downloader | `700` | Delay between API pages (ms) |
| `STORAGE_KEY` | Downloader | `grokdl_downloaded_ids` | Where download history is stored |

These are the same endpoints Grok's own web app uses; they may change if Grok updates its API.

---

## Troubleshooting

- **No tools appear** — confirm Tampermonkey is enabled and you're on `grok.com/imagine`. The search bar only shows on the Saved page; the **🏷 Tags** button only on Imagine pages.
- **Search finds nothing / Tag Manager grid is empty** — the index hasn't been built yet. Visit the Saved page and let indexing finish first.
- **Downloads do nothing** — your browser may be blocking multiple automatic downloads; allow them for grok.com. Check the script's log panel and the browser console (entries are prefixed `[GrokDL]`).
- **Duplicate buttons** — you still have one or more of the original three scripts installed. Remove them.

---

## Notes & caveats

- The toolkit talks only to Grok's own endpoints using your existing session. It stores data locally (the search index and download history) and sends nothing anywhere else.
- This is an unofficial tool and not affiliated with xAI or Grok. It depends on Grok's current web API, so a site update could break parts of it until the script is adjusted.
- Use it within Grok's terms of service.

---

## License

Released under the [MIT License](LICENSE). Copyright © 2026.
