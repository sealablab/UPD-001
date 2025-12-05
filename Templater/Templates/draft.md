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

<% tp.file.include("[[_footer]]") %>
