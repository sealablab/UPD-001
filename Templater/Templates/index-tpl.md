<%*
// index-tpl.md - Index template (type: I)
//
// Apply to existing files to add Index frontmatter.
// Timestamps handled by 00_times_startup_hook.md
//
// Note: Template named index-tpl.md to avoid confusion with INDEX.md files.

// === NAMING VALIDATION ===
const currentName = tp.file.title;
let namingNote = "";

if (currentName !== "README" && currentName !== "INDEX") {
  namingNote = `<!-- NAMING: Index documents should be named README.md or INDEX.md -->`;
}
-%>
---
type: I
status: PUBLISHED
sensitivity: public
tags:
<% namingNote %>
# scope:
---

# <% tp.file.title %>

## Contents

-

## Overview

<% tp.file.include("[[_footer]]") %>
