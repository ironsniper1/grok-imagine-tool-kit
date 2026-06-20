# Changelog

All notable changes to **Grok Imagine Toolkit** are documented here.

This project adheres loosely to [Semantic Versioning](https://semver.org/) and the
[Keep a Changelog](https://keepachangelog.com/) format. Newest releases first.

## [2.0.0] — 2026-06-19

Major release. Every tool gained real capabilities, and the dock is now yours to style. All features below were tested live except where noted.

### Added
- **Prompt templates with variables** — saved prompts containing `{placeholders}` become reusable templates (marked with a 🧩 badge). Clicking one opens a fill-in dialog with a live preview, and remembers your last-used values per template. Plain prompts work exactly as before.
- **Custom download filenames** — a `Download filename template` setting controls how saved files are named, using tokens `{tag} {id} {model} {prompt} {date} {index}`. The default reproduces the previous naming, so nothing changes unless you want it to. Empty tokens collapse cleanly and illegal characters are sanitized.
- **📦 Bundle as ZIP** — a toggle on the downloader packages a run into ZIP archives instead of loose files. Honored by both Download Favorites and Download by Tag. Uses a dependency-free, built-in ZIP writer (no external libraries) for reliability, with a configurable batch size.
- **🪄 Auto-tag by prompt** — in the Tags panel, define rules like `Witch: witch, hex, cauldron` and the toolkit creates the tags and files matching images into them via Grok's API. Preview is read-only and safe; Apply is rate-limited, idempotent (skips already-tagged images), and Stop-able.
- **📊 Dashboard** — a new dock button opens a read-only overview: total indexed images, tag count, downloads, saved prompts/templates, the date span of your library, a per-month bar chart, and your most-used prompt keywords.
- **Dock styles (Classic / Light / Compact)** — a new Settings option restyles the dock. *Classic* is unchanged (default); *Light* gives every control one consistent button language; *Compact* collapses the dock into a horizontal strip of icons with the downloader tucked into a popover. Applies on reload.
- **■ Stop button for downloads** — long Download Favorites / Download by Tag runs (including ZIP runs) can now be cancelled mid-flight. Files already saved are kept, and download history is preserved so a re-run skips what completed. Works during both the collection and download phases.
- **🗂 Manage tags — rename, delete, merge** *(new — please test before relying on it)*. A Manage button in the Tags panel opens a dialog to rename a tag, bulk-delete tags, or merge several tags into one. Deleting a tag removes only the tag — your images stay in your library. Merge reads each source tag's members fresh, skips images already in the target (safe to re-run), and only deletes a source tag once **all** of its images have moved without error; any failure keeps the tag. Rate-limited and Stop-able. Built on Grok endpoints confirmed by live testing (`folder/update`, `folder/delete`, `post/like`). Remove-image-from-tag is intentionally not included pending confirmation of its endpoint's behavior.

### Changed
- The Settings panel now supports text and dropdown options in addition to numeric tunables (used by the filename template and dock style).

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
