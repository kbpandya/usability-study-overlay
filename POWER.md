---
name: "usability-study-overlay"
displayName: "Usability Study Overlay"
description: "Add a standardized floating task panel to any prototype for running moderated usability studies. Supports multi-page configs and works with any framework."
keywords: ["usability", "study", "overlay", "usability study", "study overlay", "tasks", "moderated testing", "prototype testing", "user testing", "task panel"]
---

# Usability Study Overlay

This power helps you add a standardized usability study overlay to any prototype page. The overlay is a self-contained vanilla JS script that injects a floating task panel — no framework dependencies, no build step.

## What It Does

When activated, this power guides you to:
1. Write the `study-overlay.js` script (included below) into the project's public scripts folder
2. Add a config block and script tag to the appropriate file for the framework
3. The overlay activates only when `?study=true` is appended to the URL

## Onboarding (First-Time Setup)

When a designer first uses this power on a project, do the following:

### Step 1: Write the overlay script

Check if `public/scripts/study-overlay.js` already exists in the project.
- If it does NOT exist, create it with the EXACT contents from the "Complete Overlay Script" section at the bottom of this document. Do NOT modify, summarize, or regenerate the script — copy it exactly.
- If it DOES exist, check the version in the file header comment and compare to the version in this document. If outdated, follow the "Updating an Existing Project" section below.

### Step 2: Detect the framework and routing style

Look at the project structure to determine the framework:
- If `index.html` + `src/main.tsx` or `vite.config.ts` exists → **Vite SPA** (Sailwind, React, etc.)
- If `src/pages/*.astro` exists → **Astro** (multi-page)
- If plain `.html` files → **Static HTML**

Also check the routing style (important for activation URL):
- If the app uses `useHashLocation` or hash-based routing (`/#/page`) → **Hash routing**
- If the app uses standard path routing (`/page`) → **Path routing**

This determines WHERE the config goes and HOW the activation URL is formed.

## Updating an Existing Project

When the designer asks to update or upgrade the overlay, OR when you detect an outdated version during onboarding:

### Version Check

If `public/scripts/study-overlay.js` already exists, read the first few lines and look for the version comment (e.g., `Appian Usability Study Overlay v2.1`). Compare it to the version in the "Complete Overlay Script" section of this document (currently v4.0).

- If the existing version is older, tell the designer: "Your project has study-overlay v{old}. The latest version is v{new}. Want me to update it?"
- If the designer confirms (or explicitly asked to update), overwrite `public/scripts/study-overlay.js` with the EXACT contents from the "Complete Overlay Script" section below.
- If the versions match, tell the designer the script is already up to date.

### What Gets Updated

Only the script file (`public/scripts/study-overlay.js`) needs to be replaced. The `STUDY_CONFIG` blocks in `index.html` or `.astro` files do NOT need to change — the config format is backward compatible.

## Adding a Study Overlay to a Prototype

When the designer asks to add a study overlay to a specific prototype/page with specific tasks:

### For Vite SPAs (Sailwind, React, etc.)

The entire app runs through a single `index.html`. Use a route-map config if there are multiple prototypes, or a simple config if there's only one.

**IMPORTANT:** Route keys in the config should use the path WITHOUT the `#` prefix. The script auto-detects hash vs path routing. For example, if the app navigates to `/#/application-status`, use `/application-status` as the route key.

**Single prototype:**
```html
<!-- At end of <body>, after the React mount script -->
<script>
  window.STUDY_CONFIG = {
    title: "Study Title Here",
    tasks: [
      "Task 1 instructions",
      "Task 2 instructions",
    ]
  };
</script>
<script src="/scripts/study-overlay.js"></script>
```

**Multiple prototypes (route map):**
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

If a STUDY_CONFIG already exists in `index.html`, ADD a new route entry to the existing config object rather than creating a duplicate.

### For Astro

Each page is its own `.astro` file. Add the config and script directly to the specific page file, at the end of the body. Use `is:inline` on both script tags:

```html
<script is:inline>
  window.STUDY_CONFIG = {
    title: "Study Title Here",
    tasks: [
      "Task 1 instructions",
      "Task 2 instructions",
    ]
  };
</script>
<script is:inline src="/scripts/study-overlay.js"></script>
```

### For Static HTML

Same as Vite SPA but on the specific HTML file:

```html
<script>
  window.STUDY_CONFIG = {
    title: "Study Title Here",
    tasks: ["Task 1", "Task 2"]
  };
</script>
<script src="/scripts/study-overlay.js"></script>
```

## Config Options

Each config block supports these options:

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `title` | string | `"Usability Study"` | Header text on the overlay |
| `position` | `"right"` or `"left"` | `"right"` | Which side of the screen |
| `draggable` | boolean | `true` | Allow dragging the panel |
| `tasks` | string[] | required | Task instruction strings (HTML supported) |

## Writing Good Tasks

- Write from the participant's perspective ("You need to deploy...")
- Use HTML for formatting: `<b>`, `<ul>/<li>`, `<strong>`
- Use inline styles for lists (e.g. `style="list-style:disc;margin-left:20px;"`)
- Keep each task focused on one goal
- Don't reveal the UI path — let participants discover it

## CRITICAL: Do NOT Regenerate the Script

The `study-overlay.js` content below is a tested, locked-down artifact. When adding a study overlay:
- Write the script EXACTLY as shown below if it doesn't exist yet
- Add the config block and script tag to the appropriate file
- NEVER rewrite, summarize, or inline the overlay logic
- If behavior changes are needed, edit `public/scripts/study-overlay.js` directly

## Activation

Append `?study=true` to the page URL. Without this parameter, the overlay is completely invisible and inert.

The URL format depends on the app's routing style:
- **Path routing:** `http://localhost:5173/page-name?study=true`
- **Hash routing:** `http://localhost:5173/?study=true#/page-name`

For hash-routed apps, `?study=true` goes BEFORE the `#` fragment.

---

## Complete Overlay Script

Write the following EXACTLY to `public/scripts/study-overlay.js` when setting up a project for the first time. Do not modify this content.

```javascript
/**
 * Appian Usability Study Overlay v4.0
 * 
 * A self-contained, drop-in script for running moderated usability studies
 * on Kiro-assisted prototypes. Zero dependencies — no icon libraries needed.
 * 
 * USAGE:
 *   1. Define window.STUDY_CONFIG before this script loads
 *   2. Include this script via <script src="/scripts/study-overlay.js"></script>
 *   3. Append ?study=true to the page URL to activate
 * 
 * ACTIVATION URLS:
 *   Path routing:  http://localhost:5173/page-name?study=true
 *   Hash routing:  http://localhost:5173/?study=true#/page-name
 * 
 * SINGLE-PAGE CONFIG:
 *   window.STUDY_CONFIG = {
 *     title: "Usability Study",
 *     tasks: ["Task 1", "Task 2"]
 *   };
 * 
 * MULTI-PAGE CONFIG (for SPAs with multiple prototypes):
 *   window.STUDY_CONFIG = {
 *     "/compare-deploy": {
 *       title: "Deploy Flow Study",
 *       tasks: ["Task 1", "Task 2"]
 *     },
 *     "/case-management": {
 *       title: "Case Mgmt Study",
 *       tasks: ["Task 1", "Task 2"]
 *     }
 *   };
 * 
 * OPTIONS (per-page or top-level):
 *   title: string       — overlay header (default: "Usability Study")
 *   position: string    — "right" (default) or "left"
 *   draggable: boolean  — drag to reposition (default: true)
 *   tasks: string[]     — task instructions (HTML supported)
 */
(function () {
  'use strict';

  // --- Guard: only run when ?study=true ---
  var params = new URLSearchParams(window.location.search);
  if (params.get('study') !== 'true') {
    var hashParts = (window.location.hash || '').split('?');
    if (hashParts.length < 2 || new URLSearchParams(hashParts[1]).get('study') !== 'true') {
      return;
    }
  }

  // --- Get current route path (supports both path and hash routing) ---
  function getCurrentPath() {
    var hash = window.location.hash || '';
    if (hash.indexOf('#/') === 0) {
      return hash.split('?')[0].replace(/^#/, '').replace(/\/$/, '') || '/';
    }
    return window.location.pathname.replace(/\/$/, '') || '/';
  }

  // --- Resolve config for current route ---
  function resolveConfig() {
    var raw = window.STUDY_CONFIG || {};
    if (Array.isArray(raw.tasks)) return raw;
    var path = getCurrentPath();
    if (raw[path]) return raw[path];
    var keys = Object.keys(raw);
    for (var i = 0; i < keys.length; i++) {
      if (path.startsWith(keys[i])) return raw[keys[i]];
    }
    return null;
  }

  // --- State ---
  var currentTask = 0;
  var overlayEl = null;
  var minBtnEl = null;
  var isMinimized = false;
  var activeCfg = null;

  // --- SVG icons (inline, zero dependencies) ---
  var ICON_MINIMIZE = '<svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round"><line x1="5" y1="12" x2="19" y2="12"/></svg>';
  var ICON_TASKS = '<svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round"><line x1="4" y1="6" x2="20" y2="6"/><line x1="4" y1="12" x2="20" y2="12"/><line x1="4" y1="18" x2="14" y2="18"/><polyline points="17 16 19 18 23 14" stroke-width="2"/></svg>';

  var ICON_BACK = '<svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="19" y1="12" x2="5" y2="12"/><polyline points="12 19 5 12 12 5"/></svg>';

  // --- Build overlay DOM ---
  function buildOverlay(cfg) {
    var position = cfg.position === 'left' ? 'left' : 'right';
    var draggable = cfg.draggable !== false;
    var title = cfg.title || 'Usability Study';

    var overlay = document.createElement('div');
    overlay.id = 'study-overlay';
    overlay.setAttribute('role', 'complementary');
    overlay.setAttribute('aria-label', 'Usability study task panel');
    Object.assign(overlay.style, {
      position: 'fixed',
      top: '50%',
      transform: 'translateY(-50%)',
      width: '360px',
      backgroundColor: '#fff',
      border: '1px solid #D1D5DB',
      borderRadius: '10px',
      boxShadow: '0 10px 30px rgba(0,0,0,0.12)',
      zIndex: '9999',
      fontFamily: 'system-ui, -apple-system, sans-serif',
      overflow: 'hidden',
      cursor: 'default',
    });
    overlay.style[position] = '16px';

    overlay.innerHTML =
      '<div id="study-header" style="background:#115EBB;color:#fff;padding:12px 16px;' + (draggable ? 'cursor:grab;' : '') + '">' +
        '<div style="display:flex;justify-content:space-between;align-items:flex-start;">' +
          '<span style="font-weight:600;font-size:15px;line-height:1.3;">' + title + '</span>' +
          '<button id="study-minimize" aria-label="Minimize" style="background:rgba(255,255,255,0.15);border:none;color:#fff;cursor:pointer;padding:4px 6px;border-radius:4px;font-size:12px;flex-shrink:0;margin-left:12px;line-height:1;">' + ICON_MINIMIZE + '</button>' +
        '</div>' +
        '<span id="study-task-num" style="font-size:12px;opacity:0.80;margin-top:4px;display:block;"></span>' +
      '</div>' +
      '<div id="study-body" style="padding:20px;">' +
        '<p id="study-task-text" style="font-size:14px;color:#000;margin:0 0 20px 0;line-height:1.6;"></p>' +
        '<div id="study-buttons" style="display:flex;gap:8px;justify-content:flex-end;"></div>' +
      '</div>';

    var minBtn = document.createElement('div');
    minBtn.id = 'study-minimized';
    Object.assign(minBtn.style, {
      position: 'fixed',
      top: '50%',
      transform: 'translateY(-50%)',
      zIndex: '9999',
      display: 'none',
    });
    minBtn.style[position] = '16px';
    minBtn.innerHTML =
      '<button id="study-maximize" aria-label="Show study panel" style="background:#115EBB;color:#fff;border:none;padding:10px 14px;border-radius:8px;cursor:pointer;display:flex;align-items:center;gap:8px;box-shadow:0 4px 12px rgba(0,0,0,0.15);font-family:system-ui,sans-serif;font-size:13px;">' +
        ICON_TASKS +
        '<span id="study-min-label"></span>' +
      '</button>';

    document.body.appendChild(overlay);
    document.body.appendChild(minBtn);
    overlayEl = overlay;
    minBtnEl = minBtn;

    // --- Position syncing between overlay ↔ pill ---
    var hasDragged = false;

    function resetPosition(el) {
      el.style.top = '50%';
      el.style.transform = 'translateY(-50%)';
      el.style.left = 'auto';
      el.style.right = 'auto';
      el.style[position] = '16px';
    }

    document.getElementById('study-minimize').addEventListener('click', function () {
      var oRect = overlay.getBoundingClientRect();
      overlay.style.display = 'none';
      minBtn.style.display = 'block';
      if (!hasDragged) {
        resetPosition(minBtn);
      } else {
        if (position === 'right') {
          minBtn.style.top = oRect.top + 'px';
          minBtn.style.right = (window.innerWidth - oRect.right) + 'px';
          minBtn.style.left = 'auto';
        } else {
          minBtn.style.top = oRect.top + 'px';
          minBtn.style.left = oRect.left + 'px';
          minBtn.style.right = 'auto';
        }
        minBtn.style.transform = 'none';
      }
      isMinimized = true;
    });

    if (!draggable) {
      document.getElementById('study-maximize').addEventListener('click', function () {
        var pRect = minBtn.getBoundingClientRect();
        minBtn.style.display = 'none';
        overlay.style.display = 'block';
        if (!hasDragged) {
          resetPosition(overlay);
        } else {
          if (position === 'right') {
            overlay.style.top = pRect.top + 'px';
            overlay.style.right = (window.innerWidth - pRect.right) + 'px';
            overlay.style.left = 'auto';
          } else {
            overlay.style.top = pRect.top + 'px';
            overlay.style.left = pRect.left + 'px';
            overlay.style.right = 'auto';
          }
          overlay.style.transform = 'none';
        }
        isMinimized = false;
      });
    }

    if (draggable) {
      var header = document.getElementById('study-header');
      var isDragging = false;
      var offsetX = 0, offsetY = 0;

      header.addEventListener('mousedown', function (e) {
        if (e.target.closest('button')) return;
        isDragging = true;
        hasDragged = true;
        header.style.cursor = 'grabbing';
        var rect = overlay.getBoundingClientRect();
        overlay.style.top = rect.top + 'px';
        overlay.style.left = rect.left + 'px';
        overlay.style.right = 'auto';
        overlay.style.transform = 'none';
        offsetX = e.clientX - rect.left;
        offsetY = e.clientY - rect.top;
        e.preventDefault();
      });

      document.addEventListener('mousemove', function (e) {
        if (!isDragging) return;
        var newLeft = Math.max(0, Math.min(e.clientX - offsetX, window.innerWidth - overlay.offsetWidth));
        var newTop = Math.max(0, Math.min(e.clientY - offsetY, window.innerHeight - overlay.offsetHeight));
        overlay.style.left = newLeft + 'px';
        overlay.style.top = newTop + 'px';
      });

      document.addEventListener('mouseup', function () {
        if (isDragging) {
          isDragging = false;
          header.style.cursor = 'grab';
        }
      });

      // --- Draggable minimized pill ---
      var maxBtn = document.getElementById('study-maximize');
      var pillDragging = false;
      var pillOffX = 0, pillOffY = 0, pillStartX = 0, pillStartY = 0, pillMoved = false;
      maxBtn.style.cursor = 'grab';

      maxBtn.addEventListener('mousedown', function (e) {
        pillDragging = true;
        pillMoved = false;
        pillStartX = e.clientX;
        pillStartY = e.clientY;
        maxBtn.style.cursor = 'grabbing';
        var rect = minBtn.getBoundingClientRect();
        minBtn.style.top = rect.top + 'px';
        minBtn.style.left = rect.left + 'px';
        minBtn.style.right = 'auto';
        minBtn.style.transform = 'none';
        pillOffX = e.clientX - rect.left;
        pillOffY = e.clientY - rect.top;
        e.preventDefault();
      });

      document.addEventListener('mousemove', function (e) {
        if (!pillDragging) return;
        var dx = e.clientX - pillStartX;
        var dy = e.clientY - pillStartY;
        if (!pillMoved && Math.abs(dx) < 5 && Math.abs(dy) < 5) return;
        pillMoved = true;
        hasDragged = true;
        var newLeft = Math.max(0, Math.min(e.clientX - pillOffX, window.innerWidth - minBtn.offsetWidth));
        var newTop = Math.max(0, Math.min(e.clientY - pillOffY, window.innerHeight - minBtn.offsetHeight));
        minBtn.style.left = newLeft + 'px';
        minBtn.style.top = newTop + 'px';
      });

      document.addEventListener('mouseup', function () {
        if (pillDragging) {
          pillDragging = false;
          maxBtn.style.cursor = 'grab';
          if (!pillMoved) {
            // Was a click, not a drag — expand the panel
            var pRect = minBtn.getBoundingClientRect();
            minBtn.style.display = 'none';
            overlay.style.display = 'block';
            if (!hasDragged) {
              resetPosition(overlay);
            } else {
              if (position === 'right') {
                overlay.style.top = pRect.top + 'px';
                overlay.style.right = (window.innerWidth - pRect.right) + 'px';
                overlay.style.left = 'auto';
              } else {
                overlay.style.top = pRect.top + 'px';
                overlay.style.left = pRect.left + 'px';
                overlay.style.right = 'auto';
              }
              overlay.style.transform = 'none';
            }
            isMinimized = false;
          }
        }
      });
    }
  }

  // --- Render current task ---
  function showTask() {
    var tasks = activeCfg.tasks;
    var label = 'Task ' + (currentTask + 1) + ' of ' + tasks.length;

    document.getElementById('study-task-num').textContent = label;
    document.getElementById('study-min-label').textContent = label;
    document.getElementById('study-task-text').innerHTML = tasks[currentTask];

    var btns = document.getElementById('study-buttons');
    var backBtn = currentTask > 0 ? '<button id="study-back" aria-label="Previous task" style="padding:8px 10px;background:transparent;border:1px solid #D1D5DB;border-radius:6px;cursor:pointer;color:#4B5563;line-height:1;display:flex;align-items:center;">' + ICON_BACK + '</button>' : '';
    btns.innerHTML = backBtn + '<button id="study-done" style="padding:10px 24px;background:#15803D;color:#fff;border:none;border-radius:6px;cursor:pointer;font-size:13px;font-weight:500;font-family:inherit;">Done</button>';
    if (currentTask > 0) {
      document.getElementById('study-back').addEventListener('click', function () {
        currentTask--;
        showTask();
      });
    }
    document.getElementById('study-done').addEventListener('click', onDone);
  }

  function onDone() {
    var tasks = activeCfg.tasks;

    if (currentTask < tasks.length - 1) {
      document.getElementById('study-task-text').innerHTML = 'Great! When the facilitator prompts you, select Next Task.';
      var btns = document.getElementById('study-buttons');
      var backBtn = '<button id="study-back" aria-label="Previous task" style="padding:8px 10px;background:transparent;border:1px solid #D1D5DB;border-radius:6px;cursor:pointer;color:#4B5563;line-height:1;display:flex;align-items:center;">' + ICON_BACK + '</button>';
      btns.innerHTML = backBtn + '<button id="study-next" style="padding:10px 24px;background:#4B5563;color:#fff;border:none;border-radius:6px;cursor:pointer;font-size:13px;font-weight:500;font-family:inherit;">Next Task</button>';
      document.getElementById('study-back').addEventListener('click', function () {
        currentTask--;
        showTask();
      });
      document.getElementById('study-next').addEventListener('click', function () {
        currentTask++;
        showTask();
      });
    } else {
      document.getElementById('study-task-text').innerHTML = 'Study complete! Thank you for participating.';
      document.getElementById('study-buttons').innerHTML = '';
    }
  }

  // --- SPA route-change detection ---
  function watchRouteChanges() {
    var lastPath = getCurrentPath();

    function checkRoute() {
      var newPath = getCurrentPath();
      if (newPath !== lastPath) {
        lastPath = newPath;
        onRouteChange();
      }
    }

    window.addEventListener('popstate', checkRoute);
    window.addEventListener('hashchange', checkRoute);

    var origPush = history.pushState;
    var origReplace = history.replaceState;
    history.pushState = function () {
      origPush.apply(this, arguments);
      checkRoute();
    };
    history.replaceState = function () {
      origReplace.apply(this, arguments);
      checkRoute();
    };
  }

  function onRouteChange() {
    var newCfg = resolveConfig();
    if (!newCfg || !Array.isArray(newCfg.tasks) || newCfg.tasks.length === 0) {
      if (overlayEl) overlayEl.style.display = 'none';
      if (minBtnEl) minBtnEl.style.display = 'none';
      return;
    }
    activeCfg = newCfg;
    currentTask = 0;

    if (overlayEl) overlayEl.remove();
    if (minBtnEl) minBtnEl.remove();
    buildOverlay(activeCfg);
    showTask();
  }

  // --- Init ---
  activeCfg = resolveConfig();
  if (!activeCfg || !Array.isArray(activeCfg.tasks) || activeCfg.tasks.length === 0) {
    console.warn('[study-overlay] No tasks found for current route. Check window.STUDY_CONFIG.');
    return;
  }

  buildOverlay(activeCfg);
  showTask();

  var raw = window.STUDY_CONFIG || {};
  if (!Array.isArray(raw.tasks)) {
    watchRouteChanges();
  }

})();
```
