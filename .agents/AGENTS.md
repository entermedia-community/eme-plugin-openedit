# AGENTS.md

## Openedit Plugin Guide

### Purpose

`plugins/openedit` implements the in-page editing toolbar: the small overlay that lets logged-in
users view and edit a page's layout, content includes, and workflow state directly from the front
end. It's a real Java+bean plugin (`html/src/plugin.xml`), unlike `manager` or `catalog`.

### Folder Map

- `html/src/plugin.xml` Spring bean wiring — toolbar generators (`ToolbarGenerator`,
  `AdminToolBarGenerator`, `VelocityGenerator`) that wrap the normal page-rendering pipeline
- `code/` Java source for openedit-specific generators/handlers, plus `log4j2.properties` and
  `mimetypes.properties`
- `html/components/toolbar/` The toolbar UI itself: `toolbar.html`/`.css`, admin toolbar selector,
  plugin toolbar, editor toolbar — each with a matching `.xconf`
- `html/components/{advancedsearch,data,html,javascript,mediaviewer,results,upload}/` Shared
  editing widgets used by the toolbar and by views below
- `html/views/{assets,filemanager,import,links,reports,workflow}/` Admin-facing screens reachable
  from the toolbar (asset browser, file manager, import tool, link checker, reports, approval
  workflow)
- `html/authentication/` Login/logout/password-reset pages used when the toolbar requires a login
- `html/theme/` Openedit's own layouts/styles for its screens
- `html/scripts/oeupload.groovy` Upload handling script

### What This Plugin Owns

- The edit-mode toolbar overlay and everything needed to toggle a page between "view" and "edit"
- Content approval workflow (`html/views/workflow`)
- File/asset management screens reachable while editing a page

### Editing Rules

- New toolbar buttons/panels go in `html/components/toolbar/`, paired `.html` + `.xconf` like the
  existing `admin.html`/`admin.xconf`, `editor.html`/`editor.xconf`.
- If a toolbar feature needs new server logic, add a Java class under `code/` and register it as a
  bean in `html/src/plugin.xml` — follow the `ToolbarGenerator`/`AdminToolBarGenerator` pattern
  (wraps an existing generator via the `wraps` property) rather than replacing the pipeline.
- New admin screens reachable from the toolbar go under `html/views/<section>/`, each page as an
  `.html` + `.xconf` pair, consistent with the `workflow`/`filemanager`/`import` folders.
- Keep openedit changes scoped to editing/workflow concerns; page content and schema changes
  belong in `catalog`/`community`, not here.

### Validation Checklist

1. Rebuild Java classes if `code/` or `plugin.xml` changed.
2. Clear the page cache (or restart) after any `.xconf`/`_site.xconf` change.
3. Load a page as an editor-permissioned user and confirm the toolbar appears and the
   new button/panel works.
4. Confirm the same page renders normally (no toolbar, no errors) for a non-editor user.

### Notes For Agents

- Openedit's toolbar wraps the normal rendering pipeline (see the `wraps` property in
  `plugin.xml`) — a broken openedit bean can break page rendering site-wide, so validate carefully.
