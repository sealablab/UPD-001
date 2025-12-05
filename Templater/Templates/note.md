<%*
// note.md - Note template (type: N)
//
// Apply to existing files to add Note frontmatter.
// Timestamps handled by 00_times_startup_hook.md
//
// Notes have free-form naming - no convention enforced.
-%>
---
type: N
status: INFORMAL
sensitivity: public
tags:
# context:
# participants: []
---

# <% tp.file.title %>



## See Also

-

---
**View this document:**
- [Obsidian Publish](https://publish.obsidian.md/upd-001/<% tp.file.path(true).replace(/\.md$/, '').replace(/ /g, '%20') %>)
- [GitHub](https://github.com/sealablab/UPD-001/blob/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
- [Edit on GitHub](https://github.com/sealablab/UPD-001/edit/main/<% tp.file.path(true).replace(/ /g, '%20') %>)
