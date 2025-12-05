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

## Structure

```
Templater/
├── DOCUMENT-TYPES.md         # Type taxonomy specification
├── README.md                 # This file
├── Templates/
│   ├── 00_times_startup_hook.md  # Startup: registers timestamp listener
│   ├── base.md                   # Type selection template
│   └── user_scripts/
│       └── templater_times.js    # Timestamp management module
└── Templater_docs/           # Extended documentation
```

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
