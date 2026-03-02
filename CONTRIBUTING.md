# Contributing — Usability Study Overlay

This document is for maintainers working on the power itself. If you're a designer using the power, see [README.md](README.md).

## File Structure

```
Usability Study Overlay/
├── POWER.md           # Kiro reads this — contains instructions + embedded script
├── README.md          # User-facing docs (shows on GitHub)
├── CHANGELOG.md       # Version history
└── CONTRIBUTING.md    # Maintainer docs (this file)
```

- `POWER.md` is the only file Kiro installs. It contains both the integration instructions and the full overlay script as a code block.
- `steering/` folder can also be installed by powers, but this power doesn't use one.
- `assets/`, `src/`, and other directories are NOT installed by powers.

## Critical Constraint: Single Code Block

POWER.md must contain exactly ONE javascript code block. If the script is split across multiple code blocks, Kiro fails to consume it properly. When editing the script, always ensure it remains a single continuous ` ```javascript ... ``` ` block.

## How to Update the Overlay Script

1. Edit the script inside POWER.md under "Complete Overlay Script"
2. Bump the version in the script header comment (e.g., `Appian Usability Study Overlay v3.1`)
3. Update the version reference in the "Version Check" section of POWER.md (the `currently v3.x` text)
4. Add an entry to CHANGELOG.md
5. Commit and push to GitHub
6. Users who reinstall the power get the new version automatically
7. Users with existing projects will be prompted to update via the version check mechanism

## Version Check Mechanism

When a designer uses the power on a project that already has `public/scripts/study-overlay.js`:

1. Kiro reads the first few lines of the existing file looking for the version comment
2. Compares it to the version in the POWER.md code block
3. If outdated, tells the designer and offers to update
4. Only the script file gets replaced — `STUDY_CONFIG` blocks are backward compatible

The version check text in POWER.md says "currently vX.X" — this MUST match the version in the script header.

## Architecture

The overlay is a single self-executing IIFE (`study-overlay.js`) that:

1. Checks for `?study=true` in the URL (supports both `window.location.search` and hash-based query params)
2. Reads `window.STUDY_CONFIG` to find tasks for the current route
3. Auto-detects hash routing (`/#/page`) vs path routing (`/page`)
4. Injects a fixed-position panel with inline styles (no CSS framework needed)
5. Manages task progression, minimize/maximize, and drag (both panel and minimized pill)
6. For multi-page configs, watches for route changes (popstate, hashchange, pushState/replaceState patches)

Zero external dependencies. No icon libraries. No build step.

## Design Decisions

- **Inline styles only** — no Tailwind classes, no external CSS. The overlay must work on any framework.
- **Vanilla JS** — no React, no framework dependencies. Must work as a plain `<script>` tag.
- **`is:inline` for Astro** — Astro pages need `is:inline` on script tags to prevent bundling.
- **Hash routing support** — Sailwind uses `wouter` with `useHashLocation`, so `?study=true` goes BEFORE the `#` fragment.
- **Route keys without `#`** — STUDY_CONFIG route keys use path without `#` prefix; the script handles detection.
- **No timing/CSV** — deferred until designers validate the need. May be re-added later.
- **Header color `#0C4283`** — deep navy, distinct from Appian's `#2322f0`. All elements pass WCAG AA.

## Accessibility

All color combinations have been verified:

| Element | Foreground | Background | Ratio | Rating |
|---------|-----------|------------|-------|--------|
| Header title | white | `#0C4283` | 9.9:1 | AAA |
| Task counter | white@80% | `#0C4283` | 6.9:1 | AA |
| Minimize icon | white | button bg | 4.6:1 | AA |
| Done button | white | `#15803D` | 4.8:1 | AA |
| Next Task button | white | `#4B5563` | 7.2:1 | AAA |
| Body text | `#374151` | white | 10.3:1 | AAA |
