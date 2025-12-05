<%*
// spec.md - Specification template (type: S)
//
// Apply to existing files to add Specification frontmatter.
// Timestamps handled by 00_times_startup_hook.md

// === NAMING VALIDATION ===
const currentName = tp.file.title;
const expectedSuffix = "-spec";
let namingNote = "";

if (!currentName.endsWith(expectedSuffix)) {
  const baseName = currentName.replace(/-(spec|ref|reference|prop|guide|handoff)$/, '');
  const suggestedName = baseName + expectedSuffix;
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
}
-%>
---
type: S
status: DRAFT
sensitivity: public
tags:
<% namingNote %>
# api_version:
# supersedes:
---

# <% tp.file.title %>

This document specifies...

<% tp.file.include("[[_footer]]") %>
