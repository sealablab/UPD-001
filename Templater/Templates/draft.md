<%*
// draft.md - Draft template (type: D)
//
// Apply to existing files to add Draft frontmatter.
// Timestamps handled by 00_times_startup_hook.md

// === NAMING VALIDATION ===
const currentName = tp.file.title;
let namingNote = "";

// Draft uses PREFIX not suffix: DRAFT-*
if (!currentName.startsWith("DRAFT-")) {
  const suggestedName = "DRAFT-" + currentName;
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
}
-%>
---
type: D
status: DRAFT
sensitivity: public
tags:
<% namingNote %>
# target_type:
# blocking_issues: []
---

# <% tp.file.title %>

> **Status:** Draft - Work in progress

## See Also

-

---
**View this document:**
- [Obsidian Publish](https://publish.obsidian.md/upd-001/<% tp.file.path(true).replace(/\.md$/, '').replace(/ /g, '%20') %>)
- [GitHub](https://github.com/sealablab/UPD-001/blob/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
- [Edit on GitHub](https://github.com/sealablab/UPD-001/edit/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
