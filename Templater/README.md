---
created: 2025-11-27
modified: 2025-12-05
type: I
status: PUBLISHED
---
# Templater

Obsidian Templater configuration for UPD-001 documentation.

## Architecture

The system uses **separation of concerns** between timestamps and document types:

| Concern | Handler | Trigger |
|---------|---------|---------|
| Timestamps (`created`, `modified`, `accessed`) | `00_times_startup_hook.md` | Automatic on file open |
| Document type & status | `base.md` | Manual or on new file |

This means:
- **Timestamps are automatic** - the startup hook registers a listener that updates them whenever you open a file
- **Type selection is explicit** - you choose the document type when creating a new document

## Quick Start

### 1. Configure Templater Plugin

In Obsidian Settings → Plugins → Templater:

| Setting | Value |
|---------|-------|
| Template folder location | `Templater/Templates` |
| User scripts folder | `Templater/Templates/user_scripts` |
| Startup templates | `Templater/Templates/00_times_startup_hook.md` |

**For automatic type selection on new files** (recommended):

| Setting | Value |
|---------|-------|
| Trigger Templater on new file creation | ON |
| Enable folder templates | ON |
| Add folder template | `/` → `base` |

### 2. Create a New Document

**With auto-trigger enabled:**
1. Create a new note (any name)
2. **Type selector appears automatically**
3. Choose document type
4. Timestamps added automatically when file opens

**Manual workflow:**
1. Create a new note
2. `CMD-P` → `Templater: Insert template`
3. Select `base`
4. Choose document type from suggester

The template will:
- Set `type` and `status` based on selection
- Warn if filename doesn't match naming convention
- Provide type-specific scaffolding
- Timestamps added automatically by hook

## Document Types

| Code | Type | Naming Convention | Default Status |
|------|------|-------------------|----------------|
| `S` | Specification | `*-spec.md` | DRAFT |
| `R` | Reference | `*-ref.md` | DRAFT |
| `P` | Proposal | `*-prop.md` | PROPOSED |
| `D` | Draft | `DRAFT-*.md` | DRAFT |
| `G` | Guide | `*-guide.md` | DRAFT |
| `N` | Note | (free) | INFORMAL |
| `H` | Handoff | `*-handoff.md` | CONTEXTUAL |
| `I` | Index | `README.md` | PUBLISHED |

See [DOCUMENT-TYPES.md](DOCUMENT-TYPES.md) for full specification.

## File Inventory

### Root Files

#### [DOCUMENT-TYPES.md](DOCUMENT-TYPES.md)
Complete type taxonomy specification defining S/R/P/D/G/N/H/I document types with naming conventions and status values.

#### [MIGRATION_NOTES.md](MIGRATION_NOTES.md)
Migration guide from old Templates/ structure to new Templater/ organization with updated settings paths.

### Templates/

#### [00_times_startup_hook.md](Templates/00_times_startup_hook.md)
Startup hook that registers workspace listener for automatic created/modified/accessed timestamps.

#### [base.md](Templates/base.md)
Interactive type selector with suggester UI. Sets type/status and adds type-specific frontmatter fields.

#### [spec.md](Templates/spec.md)
Specification template (type: S). Adds api_version/supersedes fields for interface definitions.

#### [ref.md](Templates/ref.md)
Reference template (type: R). For lookup tables, API docs, register maps with derived_from field.

#### [prop.md](Templates/prop.md)
Proposal template (type: P). Includes proposal_id, decision, and decision_date fields.

#### [draft.md](Templates/draft.md)
Draft template (type: D). Work-in-progress docs with target_type and blocking_issues fields.

#### [guide.md](Templates/guide.md)
Guide template (type: G). For how-to docs and tutorials with Overview section scaffold.

#### [note.md](Templates/note.md)
Note template (type: N). Minimal frontmatter for meeting notes, observations, scratch work.

#### [handoff.md](Templates/handoff.md)
Handoff template (type: H). Session continuity with session_id, prior_handoff, next_steps fields.

#### [index-tpl.md](Templates/index-tpl.md)
Index template (type: I). For README.md/INDEX.md navigation documents with Contents section.

#### [_footer.md](Templates/_footer.md)
Shared footer partial with "See Also" section and links to Obsidian Publish, GitHub, and Edit URLs.

#### [new_vhdl_component.md](Templates/new_vhdl_component.md)
Master VHDL template. Creates paired .vhd and .vhd.md files with linked frontmatter in one step.

#### [vhdl_doc.vhd.md](Templates/vhdl_doc.vhd.md)
Template for .vhd.md documentation files. Generates frontmatter with code_link to paired .vhd file.

#### [vhdl_file.vhd](Templates/vhdl_file.vhd)
Template for .vhd source files. Generates YAML frontmatter compatible with Obsidian and HDL tools.

#### [BLANK_TOP_FILE.vhd.md](Templates/BLANK_TOP_FILE.vhd.md)
Static VHDL top-level file template. Placeholder for new component documentation.

#### [README.md](Templates/README.md)
Templates folder quick reference with usage notes and script configuration guidance.

### Templates/user_scripts/

#### [templater_times.js](Templates/user_scripts/templater_times.js)
Timestamp manager. Handles created/modified/accessed fields with 15-minute access throttling.

#### [templater_vhdl_pair.js](Templates/user_scripts/templater_vhdl_pair.js)
VHDL pair helpers. Bidirectional linking, frontmatter sync, and path resolution between .vhd/.vhd.md.

#### [templater_internal_module.js](Templates/user_scripts/templater_internal_module.js)
Legacy frontmatter module using Obsidian's fileManager API for older Templater compatibility.

### Templater_docs/

#### [TEMPLATER_CONFIG_GUIDE.md](Templater_docs/TEMPLATER_CONFIG_GUIDE.md)
Templater plugin configuration guide with recommended settings and folder template setup.

#### [TEMPLATE_FRONTMATTER_TIMES.md](Templater_docs/TEMPLATE_FRONTMATTER_TIMES.md)
Documentation for frontmatter timestamp fields: created, modified, accessed behavior and format.

#### [VHDL_PAIR_TEMPLATE_README.md](Templater_docs/VHDL_PAIR_TEMPLATE_README.md)
Complete VHDL pair template documentation with architecture and usage examples.

#### [VHDL_PAIR_QUICKSTART.md](Templater_docs/VHDL_PAIR_QUICKSTART.md)
Quick start guide for creating VHDL component pairs with step-by-step instructions.

#### [OBSIDIAN_IPAD_FILE_BROWSER_ISSUE.md](Templater_docs/OBSIDIAN_IPAD_FILE_BROWSER_ISSUE.md)
Workaround documentation for iPad file browser limitations in Obsidian mobile.

#### Archived_Templater_files/
Contains `times_debug.tar.gz` - archived debug templates for timestamp troubleshooting.

## Workflow Example

```
1. Create note: "BOOT-FSM-spec"
2. Type selector appears (auto or manual)
3. Select "S - Specification"
4. Template generates:
   - type: S
   - status: DRAFT
   - api_version/supersedes fields (commented)
   - "This document specifies..." intro
5. Hook adds: created, modified, accessed
```

When ready, change `status: DRAFT` → `status: AUTHORITATIVE`.

## See Also

- [DOCUMENT-TYPES.md](DOCUMENT-TYPES.md) - Complete type specification
- [MIGRATION_NOTES.md](MIGRATION_NOTES.md) - Migration from older templates
