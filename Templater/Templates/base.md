<%*
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
created: <% tp.date.now("YYYY-MM-DD") %>
modified: <% tp.date.now("YYYY-MM-DD HH:mm:ss") %>
accessed: <% tp.date.now("YYYY-MM-DD HH:mm:ss") %>
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

## See Also

-

---
**View this document:**
- [Obsidian Publish](https://publish.obsidian.md/upd-001/<% tp.file.path(true).replace(/\.md$/, '').replace(/ /g, '%20') %>)
- [GitHub](https://github.com/sealablab/UPD-001/blob/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
- [Edit on GitHub](https://github.com/sealablab/UPD-001/edit/main/<% tp.file.path(true).replace(/ /g, '%20') %>)

<%*
  // Touch timestamps
  if (tp.user && tp.user.templater_times) {
    await tp.user.templater_times.touch_current_file(tp);
  }
%>
