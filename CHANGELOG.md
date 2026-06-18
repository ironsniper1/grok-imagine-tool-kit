# Changelog

All notable changes to **Grok Imagine Toolkit** are documented here.

This project adheres loosely to [Semantic Versioning](https://semver.org/) and the
[Keep a Changelog](https://keepachangelog.com/) format. Newest releases first.

## [1.9.0] — 2026-06-16

### Added
- **⚙️ Settings panel** — a new dock button (above the 🧰 launcher) opens a panel for tuning the toolkit without editing the script. Exposes search results-per-page and the downloader's batch size, batch pause, per-download delay, and per-API-page delay. Values are clamped to safe ranges, persist across sessions, and apply on the next page reload. Includes **Reset to defaults**.

### Changed
- **Error resilience** — failures now surface instead of failing silently. The search indexer checks HTTP status (so a signed-out/expired session no longer looks like an empty library) and shows a clear toast such as *"Couldn't update search index: HTTP 401 (not signed in?)"* instead of leaving search quietly stale.
- The "no results" message now explains date-range emptiness — e.g. *No "pinup" images between 2025-01-01 and 2025-12-31* — so an empty date filter no longer looks like a broken search.

## [1.8.0] — 2026-06-16

### Added
- **Date filter** in search — From/To date pickers that filter saved images by creation date. Works alongside a keyword search, or on its own to browse everything in a date window.
- **🔀 Shuffle** — randomize the current search results for inspiration or reviewing a large collection.
- **⤓ Export search results** — export the current (filtered/shuffled) results to JSON (id, prompt, date, post URL, media URL) for backup or use in other scripts.
- **Import / export prompts** — the Saved Prompts panel can now export your library to JSON and import one back in (new, non-duplicate prompts merge at the top). Great for backups and sharing.
- **Toast notifications** — long/one-off actions (exports, imports) now show a brief on-screen confirmation instead of only logging to the console.

## [1.7.0] — 2026-06-16

### Changed
- **Collapsible dock** — the dock now starts as a single **🧰 Grok Toolkit** button. Click it to fan out the tools (Prompts, Tags, Download Favorites, Download by Tag, Reset); click it again or click away to collapse. Keeps the corner tidy instead of showing every button all the time.

### Fixed
- **Dock no longer covers Grok's controls on the single-image view.** After a Grok Imagine UI update, the post/edit view (`/imagine/post/...`) gained its own bottom-right controls (favorite, ••• menu, action strip). The toolkit dock now hides itself on that view and reappears on the grid/Saved pages.

## [1.6.0] — 2026-06-14

### Added
- **Saved Prompts** — a new **📝 Prompts** tool in the dock. Save prompts (type/paste them in, or grab whatever's currently in Grok's "Type to imagine" box), then click any saved prompt to type it back into Grok's prompt box — either replacing the box or appending to it (toggle, remembered across sessions). Prompts persist across sessions and can be deleted individually.

## [1.5.0] — 2026-06-14

### Changed
- **Search now matches whole words** instead of raw substrings, in **both** the main Favorites Search and the Tag Manager's in-grid search. Searching "mage" no longer drags in every prompt containing "i**mage**". Prefix typing still works (e.g. "warr" → "warrior"); only mid-word false positives are excluded. Match counts will be more accurate.

### Added
- **Auto-update support** via `@updateURL` / `@downloadURL`, pointed at the latest GitHub release asset. Install from the release link once and Tampermonkey handles future updates automatically (when `@version` is bumped on a new release).

## [1.4.0] — 2026-06-13

### Added
- **Download by Tag → "Include variations (child images/videos)"** checkbox. Off by default (downloads just the directly-tagged items, as before); tick it to also pull each item's child images and videos.

### Changed
- **Download Favorites** now also harvests the separate `images[]` / `videos[]` arrays (matching the per-card button), so it catches every child image and video — not only those nested under `childPosts` / `children` / `mediaList` / `media`. Same-id duplicates are de-duped automatically.

## [1.3.0] — 2026-06-13

### Fixed
- **Search** no longer fails to show results on the first try after clearing and re-searching. Removed a render guard that could skip the final render, so you no longer need to click Next/Prev to wake it up.
- **Tag Manager** bulk-tag dropdown now keeps your selected tag after adding images, instead of resetting to the top of the list. It stays put until you choose a different one.

## [1.2.0] — 2026-06-13

### Fixed
- **Search results overlay** now reliably paints over Grok's grid. Raised the overlay above Grok's content layer, hid Grok's native grid while results are shown so nothing leaks through, and broadened grid detection so results display in **Compact** view as well as Full.

## [1.1.0] — 2026-06-13

### Added
- **Download by Tag → "Include tag name in filename"** option, prefixing each saved file with the tag (e.g. `Nurse_1a2b3c4d_grok_a-prompt.jpg`).

### Changed
- **Reset download history** is now a solid button instead of a faint, see-through text link.
- **Search results** reworked toward a self-contained overlay so they render below the search bar instead of behind Grok's top UI, and work regardless of view mode.

### Performance
- **Tag Manager** defers its per-tag membership scan until the panel is first opened, instead of running it on every page load.
- **Bulk Downloader** throttles the per-card button observer (one pass per 200 ms) to stay light during Grok's virtual-scroll churn.

## [1.0.0] — 2026-06-13

### Added
- Initial unified release: the three previously separate userscripts — **Favorites Search**, **Tag Manager**, and **Bulk Favorites Downloader** — merged into a single toolkit.
- Shared, non-overlapping bottom-right dock for the Tag Manager and Downloader buttons.
- Single-page-app awareness so each tool appears only where it belongs (search on the Saved page, Tag Manager on Imagine pages, downloader site-wide).
- Search bar repositioned below Grok's selection toolbar to avoid overlap.

### Notes
- Replaces the standalone *Grok Imagine Favorites Search*, *Grok Imagine Tag Manager*, and *Grok Imagine Bulk Favorites Downloader* scripts — uninstall those to avoid duplicate UI.

[1.9.0]: #190--2026-06-16
[1.8.0]: #180--2026-06-16
[1.7.0]: #170--2026-06-16
[1.6.0]: #160--2026-06-14
[1.5.0]: #150--2026-06-14
[1.4.0]: #140--2026-06-13
[1.3.0]: #130--2026-06-13
[1.2.0]: #120--2026-06-13
[1.1.0]: #110--2026-06-13
[1.0.0]: #100--2026-06-13
