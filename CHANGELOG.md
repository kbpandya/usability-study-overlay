# Changelog

## v3.5 — 2026-03-02

- Header color changed to deep navy `#0C4283` — white text pops strongly (9.9:1 contrast)
- Task counter opacity restored to 0.80 (6.9:1 contrast on darker background)

## v3.4 — 2026-03-02

- Header color changed from `#115EBB` to `#1D6FD3` — brighter, more vibrant blue for better white text pop
- Task counter opacity bumped from 0.80 to 0.95 to maintain WCAG AA on lighter background

## v3.3 — 2026-03-02

- Body text size changed from 13px to 14px (0.875rem) — readable but subordinate to 15px header title

## v3.2 — 2026-03-02

- Fixed minimize causing pill to disappear — getBoundingClientRect was called after display:none
- Rect is now captured before hiding the element, then applied after showing the other
- Removed unused syncMinimize/syncMaximize/positionAbsolute functions

## v3.1 — 2026-03-02

- Fixed minimize position: pill now appears on the same side as the panel (right stays right, left stays left)
- Before any drag, minimize/maximize uses default CSS positioning (no jump to top-left)
- After dragging, position syncs based on the correct edge (right edge for right-positioned panels)

## v3.0 — 2026-03-02 (First public release)

- Header color: medium blue (`#115EBB`) with white text
- Minimized pill is draggable with position syncing to expanded panel
- All text and interactive elements pass WCAG AA contrast requirements
- Done button: `#15803D` (4.8:1 contrast)
- Task counter: white at `opacity:0.80` (4.6:1 contrast)
- Version check: Kiro detects outdated `study-overlay.js` and offers to update
- Pure task progression panel — no timing or CSV export

## v2.9 — 2026-03-02

- Minimized pill is now draggable (syncs position with expanded panel)
- 5px movement threshold distinguishes drag from click on the pill
- Minimize/maximize preserves position — panel appears where the pill was and vice versa

## v2.8 — 2026-03-02

- Header color: medium blue (`#115EBB`) with white text — distinct from Appian's `#2322f0`, stands out against white prototype backgrounds
- Task counter: white text with `opacity:0.80` (4.6:1 contrast ratio)

## v2.7 — 2026-03-02

- Header color: medium blue (`#115EBB`) with white text — distinct from Appian's `#2322f0`, stands out against white prototype backgrounds
- Accessibility fix: Done button green changed from `#22C55E` (~2.3:1 contrast) to `#15803D` (~4.8:1, passes WCAG AA)
- All text and interactive elements now pass WCAG AA contrast requirements
- Task counter: white text with `opacity:0.80` (4.6:1 contrast ratio)
- Minimize button: `rgba(255,255,255,0.15)` background with white icon
- Minimized pill: solid `#115EBB` background with white text
- Added version check to POWER.md — Kiro detects outdated `study-overlay.js` and offers to update

## v2.4 — 2026-03-02

- Removed all timing logic (startTimer, stopTimer, taskElapsed, formatTime)
- Removed CSV export (downloadResults, stripHtml, escapeCsv)
- Overlay is now a pure task progression panel — show task, Done, Next Task, complete
- Timing may be re-added in a future version once requirements are validated

## v2.3 — 2026-03-02

- Visual redesign of the overlay panel
- Header: stacked layout (title on top, task counter below) — fixes wrapping on long titles
- Header color: bright blue → dark slate (`#2D3748`) for less visual competition with prototype
- Panel border: bright blue → neutral gray (`#D1D5DB`) for subtler presence
- Panel width: 320px → 360px for better content breathing room
- Body padding: 16px → 20px
- Body text color: `#333` → `#374151` (softer)
- Done button: larger padding, softer green (`#22C55E`), matched 6px border radius
- Next Task button: neutral dark (`#4B5563`) instead of bright blue — better hierarchy
- Minimize button: larger tap target with subtle background (`rgba(255,255,255,0.15)`)
- Minimize icon: 12px → 14px
- Panel border radius: 8px → 10px
- Minimized pill: updated to match dark slate header color

## v2.2 — 2026-03-02

- Removed visible timer from participant UI (no clock icon, no duration display)
- Removed timer summary from study completion screen
- Timing now runs silently in the background for all studies
- Auto-downloads CSV results file on study completion
- CSV includes per-task durations, total time, study title, route, and timestamp
- Filename format: `study-results_<Title>_<date>_<time>.csv`
- Removed `showTimer` config option (timing always runs, never shown)
- Removed `ICON_CLOCK` SVG (no longer needed)

## v2.1 — 2026-03-01

- Added hash routing support (`/#/page-name`) for Sailwind/wouter projects
- `getCurrentPath()` helper auto-detects hash vs path routing
- `?study=true` guard now checks hash-based query params too (`/#/page?study=true`)
- Route change watcher listens for `hashchange` events
- Updated POWER.md with hash routing activation URL docs
- Packaged as a Kiro Power (POWER.md with embedded script)

## v2.0 — 2026-02-28

- Multi-page route-map config support
- SPA route-change detection (pushState/popstate patching)
- Rebuilt overlay DOM on route change (title/position can differ per route)
- Replaced Font Awesome icons with inline SVGs (zero dependencies)
- Extracted from inline Astro page code into standalone `study-overlay.js`

## v1.0 — 2026-02-15

- Initial overlay embedded in `separate-multi-app.astro`
- Single-page config only
- Task progression with Done → Next Task flow
- Per-task timers
- Minimize/maximize toggle
- Draggable panel
- Activated via `?study=true` URL parameter
