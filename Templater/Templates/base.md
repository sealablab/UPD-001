---
created: 2025-12-05
modified: 2025-12-05 15:33:32
accessed: 2025-12-05 15:36:03
---
<%*
// base.md - Document type selection template
//
// SEPARATION OF CONCERNS:
// - Timestamps (created/modified/accessed): handled by 00_times_startup_hook.md
//   which registers an active-leaf-change listener. When this file opens,
//   the hook automatically adds/updates timestamps via processFrontMatter.
// - Type & status: handled HERE via interactive selection
//
// This template focuses solely on type classification and type-specific fields.

// === TYPE SELECTION ===
const typeOptions = [
  { code: "S", name: "Specification", suffix: "-spec.md", defaultStatus: "DRAFT" },
  { code: "R", name: "Reference", suffix: "-ref.md", defaultStatus: "DRAFT" },
  { code: "P", name: "Proposal", suffix: "-prop.md", defaultStatus: "PROPOSED" },
  { code: "D", name: "Draft", prefix: "DRAFT-", defaultStatus: "DRAFT" },
  { code: "G", name: "Guide", suffix: "-guide.md", defaultStatus: "DRAFT" },
  { code: "N", name: "Note", suffix: null, defaultStatus: "INFORMAL" },
  { code: "H", name: "Handoff", suffix: "-handoff.md", defaultStatus: "CONTEXTUAL" },
  { code: "I", name: "Index", suffix: null, defaultStatus: "PUBLISHED" }
];

const typeLabels = typeOptions.map(t => `${t.code} - ${t.name}`);
const selectedLabel = await tp.system.suggester(typeLabels, typeLabels, false, "Select document type:");

// Handle cancellation (user pressed Escape or clicked away)
if (!selectedLabel) {
  new Notice("Template cancelled - no type selected");
  return "";  // Abort template, output nothing
}

const selectedType = typeOptions.find(t => selectedLabel.startsWith(t.code));

// === NAMING VALIDATION ===
const currentName = tp.file.title;
let suggestedName = currentName;
let namingNote = "";

if (selectedType.prefix && !currentName.startsWith(selectedType.prefix)) {
  suggestedName = selectedType.prefix + currentName;
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
} else if (selectedType.suffix && !currentName.endsWith(selectedType.suffix.replace('.md', ''))) {
  const baseName = currentName.replace(/-(spec|ref|reference|prop|guide|handoff)$/, '');
  suggestedName = baseName + selectedType.suffix.replace('.md', '');
  namingNote = `<!-- NAMING: Consider renaming to ${suggestedName} -->`;
}
-%>
---
type: <% selectedType.code %>
status: <% selectedType.defaultStatus %>
sensitivity: public
tags:
<% namingNote %>
<%* /* TYPE-SPECIFIC OPTIONAL FIELDS */ -%>
<% selectedType.code === "S" ? "# api_version: \n# supersedes: " : "" -%>
<% selectedType.code === "P" ? "# proposal_id: \n# decision: \n# decision_date: " : "" -%>
<% selectedType.code === "D" ? "# target_type: \n# blocking_issues: []" : "" -%>
<% selectedType.code === "H" ? "# session_id: \n# prior_handoff: \n# next_steps: []" : "" -%>
---

# <% tp.file.title %>

<%* /* Type-specific intro based on selection */ -%>
<% selectedType.code === "S" ? "This document specifies..." : "" -%>
<% selectedType.code === "R" ? "Quick reference for..." : "" -%>
<% selectedType.code === "P" ? "## Summary\n\nThis proposal..." : "" -%>
<% selectedType.code === "D" ? "> **Status:** Draft - Work in progress\n\n" : "" -%>
<% selectedType.code === "G" ? "## Overview\n\nThis guide explains how to..." : "" -%>
<% selectedType.code === "H" ? "## Context\n\n## Decisions Made\n\n## Next Steps\n\n- [ ] " : "" -%>

<% tp.file.include("[[_footer]]") %>
