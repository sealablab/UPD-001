---
created: 2025-11-27
modified: 2025-12-05
type: I
status: PUBLISHED
---
# Templater

Obsidian Templater configuration for UPD-001 documentation.

## Quick Start

### 1. Configure Templater Plugin

In Obsidian Settings → Plugins → Templater:

| Setting | Value |
|---------|-------|
| Template folder location | `Templater/Templates` |
| User scripts folder | `Templater/Templates/user_scripts` |

### 2. Create a New Document

1. Create a new note (any name)
2. `CMD-P` → `Templater: Insert template`
3. Select `base`
4. **Choose document type** from the suggester popup

The template will:
- Set appropriate frontmatter based on type
- Warn if filename doesn't match naming convention
- Provide type-specific scaffolding

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
├── DOCUMENT-TYPES.md    # Type taxonomy specification
├── README.md            # This file
├── Templates/
│   ├── base.md          # Main template with type selection
│   └── user_scripts/    # JavaScript modules
└── Templater_docs/      # Extended documentation
```

## Workflow Example

```
1. Create note: "BOOT-FSM-spec"
2. Insert base template
3. Select "S - Specification"
4. Template generates:
   - type: S
   - status: DRAFT
   - api_version/supersedes fields (commented)
   - "This document specifies..." intro
```

When ready, change `status: DRAFT` → `status: AUTHORITATIVE`.

## See Also

- [DOCUMENT-TYPES.md](DOCUMENT-TYPES.md) - Complete type specification
- [MIGRATION_NOTES.md](MIGRATION_NOTES.md) - Migration from older templates
