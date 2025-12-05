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

<% tp.file.include("[[_footer]]") %>
