---
name: add-toolbar-button
description: Use this skill when the user wants to add a new button, panel, or action to the on-page edit toolbar — requests like "add a toolbar button that does X", "add an admin action to the edit bar", or "add a new panel to the openedit toolbar". Covers the html/xconf pair and, if needed, the bean wiring. Consult this before hand-editing files under plugins/openedit/html/components/toolbar, since the toolbar wraps the main rendering pipeline and mistakes here can affect every page.
---

# Add a Toolbar Button/Panel

Adds a new control to the openedit in-page editing toolbar.

## Step 1: Add the HTML fragment

Add `html/components/toolbar/<name>.html` next to the existing ones (`admin.html`, `editor.html`,
`plugintoolbar.html`) — a small fragment included into the toolbar, not a full page.

## Step 2: Add the matching `.xconf`

Add `html/components/toolbar/<name>.xconf` to control permissions and any path-actions the button
needs, following the pattern of `admin.xconf`/`editor.xconf`.

## Step 3: Wire it into the toolbar shell

Reference the new fragment from `toolbar.html` (or `admintoolbarselector.html` for admin-only
controls) so it actually renders as part of the toolbar.

## Step 4: Add server logic only if the button needs new behavior

If the button triggers a new operation (not just navigating to an existing view), add a Java class
under `code/` and register it as a bean in `html/src/plugin.xml`. Prefer wrapping/extending an
existing generator (see the `wraps` property pattern already used by `velocity`/`wiki` beans in
`plugin.xml`) over introducing a parallel rendering path.

## Step 5: Validate

1. Rebuild Java classes if `code/` or `plugin.xml` changed.
2. Clear the page cache (or restart).
3. Load a page as an editor-permissioned user — confirm the new control appears and works.
4. Load the same page as a non-editor user — confirm the toolbar (and the new control) does not
   appear and the page still renders normally. A broken toolbar bean can break rendering
   site-wide, so this check matters more here than in most other plugins.
