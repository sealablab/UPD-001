<%*
// guide.md - Guide template (type: G)
//
// Apply to existing files to add Guide frontmatter.
// Timestamps handled by 00_times_startup_hook.md

// === NAMING VALIDATION ===
const currentName = tp.file.title;
let namingNote = "";

if (!currentName.endsWith("-guide")) {
  const baseName = currentName.replace(/-(spec|ref|reference|prop|guide|handoff)$/, '');
  const suggestedName = baseName + "-guide";
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
}
-%>
---
type: G
status: DRAFT
sensitivity: public
tags:
<% namingNote %>
# audience:
# prerequisites: []
# related_specs: []
---

# <% tp.file.title %>

## Overview

This guide explains how to...

## Prerequisites

## Steps

### 1.

### 2.

### 3.

## Troubleshooting

## See Also

-

---
**View this document:**
- [Obsidian Publish](https://publish.obsidian.md/upd-001/<% tp.file.path(true).replace(/\.md$/, '').replace(/ /g, '%20') %>)
- [GitHub](https://github.com/sealablab/UPD-001/blob/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
- [Edit on GitHub](https://github.com/sealablab/UPD-001/edit/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
