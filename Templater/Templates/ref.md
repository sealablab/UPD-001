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

<% tp.file.include("[[_footer]]") %>
