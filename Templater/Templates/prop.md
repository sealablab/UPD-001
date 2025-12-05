<%*
// prop.md - Proposal template (type: P)
//
// Apply to existing files to add Proposal frontmatter.
// Timestamps handled by 00_times_startup_hook.md

// === NAMING VALIDATION ===
const currentName = tp.file.title;
let namingNote = "";

if (!currentName.endsWith("-prop")) {
  const baseName = currentName.replace(/-(spec|ref|reference|prop|guide|handoff)$/, '');
  const suggestedName = baseName + "-prop";
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
}
-%>
---
type: P
status: PROPOSED
sensitivity: public
tags:
<% namingNote %>
# proposal_id:
# decision:
# decision_date:
---

# <% tp.file.title %>

## Summary

This proposal...

## Motivation

## Proposed Solution

## Alternatives Considered

## See Also

-

---
**View this document:**
- [Obsidian Publish](https://publish.obsidian.md/upd-001/<% tp.file.path(true).replace(/\.md$/, '').replace(/ /g, '%20') %>)
- [GitHub](https://github.com/sealablab/UPD-001/blob/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
- [Edit on GitHub](https://github.com/sealablab/UPD-001/edit/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
