# Usability Study Overlay — Kiro Power

A drop-in floating task panel for running moderated usability studies on any web prototype. Install once as a Kiro Power, then just tell Kiro which page and tasks you want — it handles the rest.

![Overlay screenshot placeholder]

## What It Does

- Injects a floating task panel on any prototype page
- Task progression with Done / Next Task flow
- Draggable, minimizable, zero dependencies
- Light, neutral styling that doesn't compete with the prototype
- Supports path routing (`/page`) and hash routing (`/#/page`)
- Multi-page configs for sites with multiple prototypes
- Only activates when `?study=true` is in the URL — invisible otherwise
- All text and interactive elements pass WCAG AA contrast requirements

## Quick Start

### 1. Install the Power

In Kiro, open the Powers panel and click "Add Custom Power" → "Import from Folder", then select this folder.

Or if this is hosted on GitHub, use "Import from GitHub URL" with the repo URL.

### 2. Use It

Open a chat in any project and say something like:

> Using the usability study overlay power, add an overlay to my /compare-deploy page with these tasks:
> 1. Find the UTIL Rules app and start a deployment
> 2. Review the deployment contents

Kiro will:
- Write the `study-overlay.js` script to `public/scripts/` (first time only)
- Add the config block and script tag to the right file for your framework
- Tell you the activation URL

### 3. Activate

Append `?study=true` to the page URL:

| Routing Style | URL Format |
|---------------|------------|
| Path routing | `http://localhost:5173/page-name?study=true` |
| Hash routing | `http://localhost:5173/?study=true#/page-name` |

## Config Reference

The overlay reads from a `window.STUDY_CONFIG` object. You can set it up as a single config or a route map.

### Single Prototype

```html
<script>
  window.STUDY_CONFIG = {
    title: "My Usability Study",
    tasks: [
      "Task 1 instructions here",
      "Task 2 instructions here"
    ]
  };
</script>
<script src="/scripts/study-overlay.js"></script>
```

### Multiple Prototypes (Route Map)

```html
<script>
  window.STUDY_CONFIG = {
    "/compare-deploy": {
      title: "Deploy Flow Study",
      tasks: ["Task 1", "Task 2"]
    },
    "/case-management": {
      title: "Case Mgmt Study",
      tasks: ["Task 1", "Task 2"]
    }
  };
</script>
<script src="/scripts/study-overlay.js"></script>
```

Route keys use the path without `#` — the script auto-detects hash vs path routing.

### Config Options

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `title` | string | `"Usability Study"` | Header text on the panel |
| `position` | `"right"` \| `"left"` | `"right"` | Which side of the screen |
| `draggable` | boolean | `true` | Allow dragging the panel |
| `tasks` | string[] | (required) | Task instructions — HTML supported |



### Task Formatting

Tasks support HTML for rich formatting:

```javascript
tasks: [
  "Find the <strong>UTIL Rules</strong> app and start a deployment",
  `Select these packages:<ul style="list-style:disc;margin-left:20px;margin-top:8px;">
    <li>CD-1892: Utility Function Refactor</li>
    <li>CD-1890: Extended Module Updates</li>
  </ul>`
]
```

Use inline styles for lists since the overlay doesn't load any CSS framework.

## Framework Notes

| Framework | Config Location | Notes |
|-----------|----------------|-------|
| Vite SPA (Sailwind, React) | `index.html` at end of `<body>` | Use route map for multiple prototypes |
| Astro | Each `.astro` page file | Add `is:inline` to both script tags |
| Static HTML | The specific `.html` file | Same as Vite SPA |

## Example Prompts

Copy-paste these into Kiro chat:

**First time setup + add a study:**
> Using the usability study overlay power, add a study overlay to my /application-status page with these tasks:
> 1. Find the pending application and open it
> 2. Review the application details and approve it

**Add to an existing project (script already exists):**
> Using the usability study overlay power, add a study to my /compare-deploy page with these 3 tasks:
> 1. Select UTIL Rules and Core Interfaces for deployment
> 2. Review the deployment contents for each app
> 3. Check the database scripts tab

**Multiple pages at once:**
> Using the usability study overlay power, set up studies for two pages:
> - /compare-deploy: "Deploy Flow Study" with tasks: [task 1], [task 2]
> - /view-packages: "Package Review Study" with tasks: [task 1], [task 2]

## Writing Good Tasks

- Write from the participant's perspective ("You need to deploy...")
- Don't reveal the UI path — let participants discover it
- Keep each task focused on one goal
- Use HTML for formatting (`<strong>`, `<ul>/<li>`)
- Use inline styles for lists (`style="list-style:disc;margin-left:20px;"`)

## Updating the Power

The overlay script is embedded directly in `POWER.md` as a single code block. To update:

1. Edit the script in `POWER.md` under "Complete Overlay Script"
2. Bump the version in the script header comment
3. Add an entry to `CHANGELOG.md`
4. Anyone who has the power installed needs to uninstall and reinstall to get the update

### Automatic Version Detection

When a designer uses the power on a project that already has `study-overlay.js`, Kiro reads the version comment in the file header and compares it to the version embedded in `POWER.md`. If the project's script is outdated, Kiro tells the designer and offers to update it. Only the script file gets replaced — `STUDY_CONFIG` blocks are backward compatible and don't need changes.

### File Structure

```
Usability Study Overlay/
├── POWER.md        # Kiro reads this — contains instructions + embedded script
├── README.md       # Human-facing docs (this file)
└── CHANGELOG.md    # Version history
```

- `POWER.md` is the only file Kiro installs. It contains both the integration instructions and the full script as a code block.
- Do NOT split the script into a separate file — powers only install `POWER.md` and `steering/`.

## How It Works (Architecture)

The overlay is a single self-executing IIFE (`study-overlay.js`) that:

1. Checks for `?study=true` in the URL (supports both `window.location.search` and hash-based query params)
2. Reads `window.STUDY_CONFIG` to find tasks for the current route
3. Auto-detects hash routing (`/#/page`) vs path routing (`/page`)
4. Injects a fixed-position panel with inline styles (no CSS framework needed)
5. Manages task progression, minimize/maximize, and drag
6. For multi-page configs, watches for route changes (popstate, hashchange, pushState/replaceState patches)

Zero external dependencies. No icon libraries. No build step.
