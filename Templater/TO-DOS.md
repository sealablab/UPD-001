# Templater TO-DOs

## Document Types Review & Mobile Startup Hook - 2025-12-05 16:29

- **Review document type structure** - Audit actual contents of document data-types to verify they match specification. **Problem:** Need to confirm the various type templates (S/R/P/D/G/N/H/I) have correct structure and fields as defined in DOCUMENT-TYPES.md. **Files:** `Templates/spec.md`, `Templates/ref.md`, `Templates/prop.md`, `Templates/draft.md`, `Templates/guide.md`, `Templates/note.md`, `Templates/handoff.md`, `Templates/index-tpl.md`, `DOCUMENT-TYPES.md`.

- **Debug mobile startup hook failure** - Investigate why 00_times_startup_hook.md doesn't trigger on mobile Obsidian. **Problem:** The startup hook that registers the workspace listener for automatic timestamps doesn't seem to run on iPad/mobile Obsidian, causing created/modified/accessed fields to not update. **Files:** `Templates/00_times_startup_hook.md:1-6`, `Templates/user_scripts/templater_times.js:13-15` (hookRegistered flag), `Templater_docs/OBSIDIAN_IPAD_FILE_BROWSER_ISSUE.md` (may be related). **Solution:** Check if Templater mobile has startup template support; may need alternative trigger method or manual workaround for mobile.
