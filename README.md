# Usability Study Overlay — Kiro Power

A drop-in floating task panel for running moderated usability studies on any web prototype. Install once as a Kiro Power, then just tell Kiro which page and tasks you want — it handles the rest.

## What It Does

- Injects a floating task panel on any prototype page
- Task progression with Done / Next Task flow
- Draggable and minimizable — both the panel and the minimized pill
- Supports path routing (`/page`) and hash routing (`/#/page`)
- Multi-page configs for sites with multiple prototypes
- Only activates when `?study=true` is in the URL — invisible otherwise
- Automatic version detection — Kiro will let you know if your project's overlay is outdated and offer to update it
- Accessible — all text and interactive elements pass WCAG AA contrast requirements

## Quick Start

### 1. Install the Power

In Kiro, open the Powers panel and click "Add Custom Power" → "Import from GitHub URL", then paste:

```
https://github.com/kbpandya/usability-study-overlay
```

![Kiro Powers panel](Kiro%20Power%20Panel%20for%20setup.png)

### 2. Use It

Open a chat in any project and say something like:

> Using the usability study overlay power, add an overlay to my /compare-deploy page with these tasks:
> 1. Find the UTIL Rules app and start a deployment
> 2. Review the deployment contents

Kiro will:
- Write the overlay script to your project (first time only)
- Add the config block and script tag to the right file for your framework
- Tell you the activation URL

### 3. Activate

Append `?study=true` to the page URL:

| Routing Style | URL Format |
|---------------|------------|
| Path routing | `http://localhost:5173/page-name?study=true` |
| Hash routing | `http://localhost:5173/?study=true#/page-name` |

## Updating the Power

When a new version is released, you'll need to reinstall the power to get the latest changes:

1. In the Powers panel, remove the existing "Usability Study Overlay" power
2. Re-add it using "Add Custom Power" → "Import from GitHub URL" with the same URL
3. Open a chat and say:

> The usability study overlay power has been updated, can you update my overlay?

Kiro will detect the version difference and replace the script file in your project. Your study configs stay untouched.

## Example Prompts

Copy-paste these into Kiro chat:

**First time setup + add a study:**
> Using the usability study overlay power, add a study overlay to my /application-status page with these tasks:
> 1. Find the pending application and open it
> 2. Review the application details and approve it

**Add to an existing project:**
> Using the usability study overlay power, add a study to my /compare-deploy page with these 3 tasks:
> 1. Select UTIL Rules and Core Interfaces for deployment
> 2. Review the deployment contents for each app
> 3. Check the database scripts tab

**Multiple pages at once:**
> Using the usability study overlay power, set up studies for two pages:
> - /compare-deploy: "Deploy Flow Study" with tasks: [task 1], [task 2]
> - /view-packages: "Package Review Study" with tasks: [task 1], [task 2]

## Config Options

Kiro sets these up for you, but here's what's available if you want to tweak them:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `title` | string | `"Usability Study"` | Header text on the panel |
| `position` | `"right"` \| `"left"` | `"right"` | Which side of the screen |
| `draggable` | boolean | `true` | Allow dragging the panel |
| `tasks` | string[] | (required) | Task instructions — HTML supported |

## Writing Good Tasks

- Write from the participant's perspective ("You need to deploy...")
- Don't reveal the UI path — let participants discover it
- Keep each task focused on one goal
- Use HTML for formatting (`<strong>`, `<ul>/<li>`)
- Use inline styles for lists (`style="list-style:disc;margin-left:20px;"`)

## Framework Support

| Framework | Notes |
|-----------|-------|
| Vite SPA (Sailwind, React) | Route map config for multiple prototypes |
| Astro | Per-page config with `is:inline` script tags |
| Static HTML | Config on the specific HTML file |

Kiro auto-detects your framework and sets things up accordingly.
