# Changelog

All notable changes to **Grok Imagine Toolkit** are documented here.

This project adheres loosely to [Semantic Versioning](https://semver.org/) and the
[Keep a Changelog](https://keepachangelog.com/) format. Newest releases first.

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

[1.4.0]: #140--2026-06-13
[1.3.0]: #130--2026-06-13
[1.2.0]: #120--2026-06-13
[1.1.0]: #110--2026-06-13
[1.0.0]: #100--2026-06-13
