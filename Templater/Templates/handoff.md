<%*
// handoff.md - Handoff template (type: H)
//
// Apply to existing files to add Handoff frontmatter.
// Timestamps handled by 00_times_startup_hook.md

// === NAMING VALIDATION ===
const currentName = tp.file.title;
let namingNote = "";

if (!currentName.endsWith("-handoff")) {
  const baseName = currentName.replace(/-(spec|ref|reference|prop|guide|handoff)$/, '');
  const suggestedName = baseName + "-handoff";
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
}
-%>
---
type: H
status: CONTEXTUAL
sensitivity: public
tags:
<% namingNote %>
# session_id:
# prior_handoff:
# next_steps: []
# blocking: []
---

# <% tp.file.title %>

## Context

## Decisions Made

## Current State

## Next Steps

- [ ]

## Blocking Issues

<% tp.file.include("[[_footer]]") %>
