<%*
// ref.md - Reference template (type: R)
//
// Apply to existing files to add Reference frontmatter.
// Timestamps handled by 00_times_startup_hook.md

// === NAMING VALIDATION ===
const currentName = tp.file.title;
let namingNote = "";

if (!currentName.endsWith("-ref") && !currentName.endsWith("-reference")) {
  const baseName = currentName.replace(/-(spec|ref|reference|prop|guide|handoff)$/, '');
  const suggestedName = baseName + "-ref";
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
}
-%>
---
type: R
status: DRAFT
sensitivity: public
tags:
<% namingNote %>
# derived_from:
# format: table
---

# <% tp.file.title %>

Quick reference for...

## See Also

-

---
**View this document:**
- [Obsidian Publish](https://publish.obsidian.md/upd-001/<% tp.file.path(true).replace(/\.md$/, '').replace(/ /g, '%20') %>)
- [GitHub](https://github.com/sealablab/UPD-001/blob/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
- [Edit on GitHub](https://github.com/sealablab/UPD-001/edit/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
