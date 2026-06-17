# Grok Imagine Toolkit

A single [Tampermonkey](https://www.tampermonkey.net/) userscript that adds four independent power tools to [Grok Imagine](https://grok.com/imagine):

1. **Favorites Search** — full-text prompt search across all your saved images, backed by a local index.
2. **Tag Manager** — organize your saved images into Grok's native tags (folders) from a searchable grid, with per-image and bulk tagging.
3. **Bulk Favorites Downloader** — download your entire favorites library (or a single tag) in batches, with download history so you never grab the same file twice.
4. **Saved Prompts** — a personal prompt library you can save to and re-insert into Grok's prompt box with one click.

All four run side by side without fighting over screen space: the Tag Manager, Downloader, and Saved Prompts buttons live in one tidy bottom-right dock that collapses to a single **🧰 Grok Toolkit** launcher, and the search bar floats top-center clear of Grok's own toolbar.

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
  - [Saved Prompts](#saved-prompts-1)
- [How it works](#how-it-works)
- [Performance](#performance)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [Notes & caveats](#notes--caveats)
- [License](#license)

---

## Features

### Favorites Search
- **Floating search bar** on the **Saved** page (`/imagine/saved`), top-center and clear of Grok's own selection toolbar.
- **Local prompt index** — builds an [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) store (`GrokSearchIndex`) of every saved image and its prompt.
- **First-time full index** of your entire saved library, with a live status readout in the bar.
- **Incremental updates** — later visits only fetch images newer than what's already stored, stopping as soon as they hit a known one.
- **Multi-term search** — every term must match the prompt.
- **Newest / oldest sort** toggle.
- **Date filter** — From/To date pickers that narrow results by creation date; works with a keyword search or on its own.
- **🔀 Shuffle** — randomize the current results for inspiration or reviewing a large collection.
- **⤓ Export results** — export the current (filtered/shuffled) view to JSON (id, prompt, date, post URL, media URL).
- **Paginated results grid** with first / prev / next / last controls and a match count.
- **Real-link result cards** — middle-click, Ctrl/Cmd-click, and "copy link" all work natively.
- **Keyboard shortcuts** — `Ctrl/Cmd+F` to focus, `Esc` to blur, `←` / `→` to page through results.
- **Clear button** to reset the query in one click.

### Tag Manager
- **🏷 Tags launcher** that opens a full-screen, searchable image grid.
- **Reads the search index** (`GrokSearchIndex`, read-only) so it shows the same library Search knows about.
- **Writes to Grok's native tags (folders)** through Grok's own API — your tags show up in Grok itself, not just here.
- **Create new tags** from the toolbar or inline while tagging.
- **Per-image tagging** via a popover that shows current membership and lets you add to any tag.
- **Bulk select + tag** — multi-select images and add them all to a tag at once.
- **Sidebar filters** — **All**, **Untagged**, and one entry per tag, each with a live count.
- **In-grid prompt search** to narrow the view.
- **Paginated grid** (first / prev / next / last).
- **Reload (↻)** to pull fresh tags and counts from Grok.
- **Deferred counting** — the heavy per-tag membership scan only runs the first time you open the panel, not on page load.

### Bulk Favorites Downloader
- **Docked button stack** — **⬇ Download Favorites**, **🏷 Download by Tag**, a live status log, and a **🗑 Reset download history** button.
- **Chunked downloads** — 200 files per batch with a short pause between batches to avoid overwhelming the browser.
- **Download history** — remembers every file it has grabbed (in script storage) and skips them next time.
- **Action modal** for the main download:

  | Option | What it does |
  | --- | --- |
  | Download new favorites | Fetch everything, skip already-downloaded, save the rest |
  | Save IDs only | Mark items as "already have" without downloading (full scan) |
  | Quick sync IDs | Same, but only scans the newest *N* pages (fast, for multi-device use) |
  | Download everything | Re-download all favorites, ignoring history |

- **Media-type filter** — images only, videos only, or both.
- **Max-pages limit** — cap how far back the fetch goes.
- **Download by Tag** — download only the items inside a chosen tag, with:
  - a **"skip already downloaded"** toggle,
  - a **media-type filter**, and
  - an **"include tag name in filename"** toggle that prefixes each file with the tag (e.g. `Nurse_1a2b3c4d_grok_a-prompt.jpg`).
- **Per-card download button** — a **⬇** appears on hover over any image and downloads that post plus its variations directly. It identifies posts via React fiber props, so it works even when the thumbnail is a base64 blob with no URL, falling back through API, embedded blob, and CDN-URL strategies.
- **Reset download history** — a solid button (with confirmation) that clears the memory so a future run re-fetches everything.
- **Organized output** — files land in `Downloads/grok-favorites/`, named `{id}_{model}_{prompt}.{ext}` (with an optional tag prefix).

### Saved Prompts
- **📝 Prompts launcher** in the dock opens a panel with your saved-prompt library.
- **Save manually** — type or paste any prompt and save it.
- **Save current** — grab whatever's currently in Grok's "Type to imagine" box and save it.
- **One-click insert** — click a saved prompt to type it into Grok's prompt box, either **replacing** what's there or **appending** to it (toggle, remembered across sessions).
- **Persistent** — prompts are stored across sessions (newest first, exact duplicates de-duped) and can be deleted individually.
- **Import / export** — back up or share your library as a JSON file; importing merges new, non-duplicate prompts in at the top.

### Shared
- **One collapsible dock** (`#grok-toolkit-dock`, bottom-right) that starts as a single **🧰 Grok Toolkit** button. Click it to reveal the Tag Manager, Downloader, and Saved Prompts buttons; click it again or click away to collapse — so nothing piles up or covers Grok's UI.
- **Single-page-app aware** — watches Grok's in-app navigation and shows each tool only where it belongs (search on Saved, Tag Manager / Saved Prompts on Imagine pages, downloader site-wide). The whole dock hides itself on the single-image view (`/imagine/post/...`), where Grok has its own bottom-right controls, and reappears on the grid/Saved pages.
- **Collision-free by design** — each tool uses its own ID/CSS prefix (`grok-*`, `gtm-*`, `grokdl-*`, `grokpr-*`) and runs in its own scope.
- **Throttled card watcher** — the per-card download buttons are added by a debounced DOM observer (one pass per 200 ms) to stay light during Grok's constant virtual-scroll churn.
- **Toast notifications** — brief on-screen confirmations for one-off actions like exports and imports.

---

## Requirements

- A browser with a userscript manager. **Tampermonkey** is recommended — the script relies on `GM_download`, `GM_xmlhttpRequest`, `GM_setValue`, and `GM_getValue`.
- An active, logged-in [grok.com](https://grok.com) session (the script uses your existing cookies; it never asks for credentials).
- Some saved/favorited images in Grok Imagine to work with.

---

## Installation

### Step 1 — Install Tampermonkey

First, install the Tampermonkey browser extension if you haven't already:

- **Chrome** → [Install from Chrome Web Store](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo)
- **Firefox** → [Install from Firefox Add-ons](https://addons.mozilla.org/firefox/addon/tampermonkey/)
- **Edge** → [Install from Edge Add-ons](https://microsoftedge.microsoft.com/addons/detail/tampermonkey/iikmkjmpaadaobahmlepeloendndfphd)
- **Safari** → [Install from App Store](https://www.tampermonkey.net/?browser=safari)

### Step 2 — Configure Tampermonkey (do this *before* installing the script)

After installing Tampermonkey, you need to enable two settings or the script will not work.

1. Go to `chrome://extensions` in your address bar.
2. Find **Tampermonkey** and click **Details**.
3. Scroll down and turn on **"Allow User Scripts"**.

This allows Tampermonkey to run scripts that have not been reviewed by Google. You should only enable this if you trust the script you are installing.

4. On the same page, also turn on **"Allow in Incognito"** if you want the script to work in private/incognito windows.

Without this, the script will only run in normal browser windows.

> **Note:** Steps 1–3 above describe Chrome. On Edge use `edge://extensions`; on Firefox and Safari these toggles aren't required.

Once both settings are enabled, proceed to installation below.

### Step 3 — Install the script

1. Open `Grok-Imagine-Toolkit.user.js` (e.g. the **raw** file link in this repo). Tampermonkey will detect it and show an install page.
2. Click **Install**.
3. Open [grok.com/imagine](https://grok.com/imagine) and the tools appear automatically.

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
Click **⬇ Download Favorites** and choose an action from the modal, or **🏷 Download by Tag** to grab a single tag — the tag dialog also lets you skip already-downloaded items, filter by media type, and **include the tag name in each filename**. Hover any image and click its **⬇** to download just that one. Use **🗑 Reset download history** if you want a future run to re-fetch everything.

### Saved Prompts
Click **📝 Prompts** to open the library. Type or paste a prompt and hit **＋ Save**, or click **⤵ Save current** to store whatever's in Grok's "Type to imagine" box. Click any saved prompt to drop it into Grok's prompt box, or **🗑** to delete it. Tick **Append to prompt box** if you'd rather add to the existing text instead of replacing it.

---

## How it works

The script is one outer wrapper containing four self-contained modules, each keeping its original logic intact. Two small pieces are shared:

- **`#grok-toolkit-dock`** — a fixed bottom-right column that the Tag Manager, Downloader, and Saved Prompts buttons are placed into, so they stack neatly instead of overlapping each other or Grok's UI.
- **A single-page-app path watcher** — Grok changes the URL without reloading, so the toolkit watches for navigation and shows/hides the right tools per page (the search bar only on Saved, the Tag Manager and Saved Prompts only on Imagine pages, and the whole dock hidden on the single-image `/imagine/post/...` view so it doesn't cover Grok's own controls).

The **Favorites Search** module owns the `GrokSearchIndex` IndexedDB store; the **Tag Manager** reads from it. The **Downloader** keeps its own download history in script storage (`grokdl_downloaded_ids`), and **Saved Prompts** stores its library in `grok_saved_prompts`. The modules don't otherwise share state, and each uses its own ID/CSS prefix (`grok-*`, `gtm-*`, `grokdl-*`, `grokpr-*`) to avoid collisions.

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
- **"Browser API Downloads — Click OK to allow Tampermonkey to start instant downloads" keeps popping up** — this prompt comes from Tampermonkey, not the script. The bulk downloader uses *instant* downloads (no Save As dialog), and Tampermonkey's **Browser API** download mode asks for the optional download permission before each batch. To stop it, open the Tampermonkey dashboard → **Settings** → set *Config mode* to **Advanced** if needed → change **Download Mode** from **Browser API** to **Native**. Per Tampermonkey, switching to Native disables this prompt (it may install a small native download helper the first time). Native mode is also more reliable when downloading many files quickly. Alternatively, just click **OK** and grant the permission once. 
- **Duplicate buttons** — you still have one or more of the original three scripts installed. Remove them.

---

## Notes & caveats

- The toolkit talks only to Grok's own endpoints using your existing session. It stores data locally (the search index and download history) and sends nothing anywhere else.
- This is an unofficial tool and not affiliated with xAI or Grok. It depends on Grok's current web API, so a site update could break parts of it until the script is adjusted.
- Use it within Grok's terms of service.

---

## License

Released under the [MIT License](LICENSE). Copyright © 2026.
