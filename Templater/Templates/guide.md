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

<% tp.file.include("[[_footer]]") %>
