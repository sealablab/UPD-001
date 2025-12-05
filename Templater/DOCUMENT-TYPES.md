---
created: 2025-12-05
modified: 2025-12-05
status: AUTHORITATIVE
type: S
---
# Document Types Specification

This document defines the official document type taxonomy for UPD-001 documentation.

## Overview

Every document has two orthogonal classification axes:

1. **Type** (`type:`) - What kind of document is this?
2. **Status** (`status:`) - What is its current maturity/authority level?

These are independent: a Specification can be in DRAFT status while being reviewed.

---

## Document Types

### Type Codes

| Code | Name | Naming Convention | Purpose |
|------|------|-------------------|---------|
| `S` | Specification | `*-spec.md` | Source of truth for interfaces, FSMs, protocols |
| `R` | Reference | `*-ref.md` or `*-reference.md` | Lookup tables, API docs, register maps |
| `P` | Proposal | `*-prop.md` | Ideas under consideration, not yet accepted |
| `D` | Draft | `DRAFT-*.md` | Work in progress, incomplete |
| `G` | Guide | `*-guide.md` | How-to docs, tutorials, workflows |
| `N` | Note | Free-form | Meeting notes, observations, scratch |
| `H` | Handoff | `*-handoff.md` | Session continuity, AI context transfer |
| `I` | Index | `README.md` or `INDEX.md` | Navigation, links to other docs |

### Type Definitions

#### S - Specification

**Purpose:** Authoritative technical specifications that define how things work.

**Characteristics:**
- Defines interfaces, protocols, state machines, encoding schemes
- Should be the single source of truth for its domain
- Changes require review and version tracking

**Naming:** `{subject}-spec.md`
- Examples: `BOOT-FSM-spec.md`, `HVS-encoding-spec.md`, `BIOS-interface-spec.md`

**Typical Status:** `AUTHORITATIVE`, `DRAFT`, `DEPRECATED`

**Optional Frontmatter:**
```yaml
api_version: "4.0"           # If specifying an API
supersedes: "BOOT-v3-spec"   # If replacing older spec
implements: "RFC-1234"       # External reference
```

---

#### R - Reference

**Purpose:** Lookup tables, quick-reference materials, API documentation.

**Characteristics:**
- Tabular data, register maps, state tables
- Optimized for lookup, not narrative reading
- Often derived from specifications

**Naming:** `{subject}-ref.md` or `{subject}-reference.md`
- Examples: `BOOT-CR0-ref.md`, `HVS-state-reference.md`, `api-v4-ref.md`

**Typical Status:** `AUTHORITATIVE`, `DRAFT`

**Optional Frontmatter:**
```yaml
derived_from: "BOOT-FSM-spec"  # Source specification
format: "table"                # Primary format (table, list, etc.)
```

---

#### P - Proposal

**Purpose:** Ideas being proposed for consideration.

**Characteristics:**
- Not yet accepted or implemented
- May include rationale, alternatives, trade-offs
- Requires explicit acceptance to become spec/implementation

**Naming:** `{subject}-prop.md`
- Examples: `BIOS-interface-prop.md`, `buffer-allocation-prop.md`

**Typical Status:** `PROPOSED`, `ACCEPTED`, `REJECTED`, `SUPERSEDED`

**Optional Frontmatter:**
```yaml
proposal_id: "PROP-2025-001"  # Tracking ID
decision_date: null           # When decided
decision: null                # ACCEPTED, REJECTED, DEFERRED
implements_to: null           # Resulting spec if accepted
```

---

#### D - Draft

**Purpose:** Work in progress, incomplete documents.

**Characteristics:**
- Explicitly incomplete or under active development
- May contain TODOs, placeholders, @CLAUDE markers
- Not authoritative until promoted

**Naming:** `DRAFT-{subject}.md`
- Examples: `DRAFT-boot-shell-architecture.md`, `DRAFT-new-feature.md`

**Typical Status:** `DRAFT`, `WIP`

**Optional Frontmatter:**
```yaml
target_type: "S"              # Intended type when complete (S, G, etc.)
blocking_issues: []           # What's preventing completion
estimated_completion: null    # Optional target date
```

---

#### G - Guide

**Purpose:** How-to documentation, tutorials, workflows.

**Characteristics:**
- Task-oriented, procedural
- Teaches readers how to accomplish something
- May reference specs but focuses on application

**Naming:** `{subject}-guide.md`
- Examples: `hardware-debug-guide.md`, `cocotb-testing-guide.md`

**Typical Status:** `PUBLISHED`, `DRAFT`, `OUTDATED`

**Optional Frontmatter:**
```yaml
audience: "developers"        # Target audience
prerequisites: []             # Required knowledge/setup
related_specs: []             # Referenced specifications
```

---

#### N - Note

**Purpose:** Informal notes, observations, meeting notes.

**Characteristics:**
- No formal structure required
- May be transient or reference material
- Not authoritative

**Naming:** Free-form (no enforced convention)
- Examples: `meeting-2025-12-05.md`, `observations.md`, `quick-notes.md`

**Typical Status:** `INFORMAL`, `ARCHIVED`

**Optional Frontmatter:**
```yaml
context: "sprint-42"          # What prompted this note
participants: []              # For meeting notes
```

---

#### H - Handoff

**Purpose:** Session continuity documents for AI context transfer.

**Characteristics:**
- Captures state at end of work session
- Enables continuation in fresh context
- Contains decisions made, context, next steps

**Naming:** `{subject}-handoff.md`
- Examples: `boot-implementation-handoff.md`, `20251205-session-handoff.md`

**Typical Status:** `CONTEXTUAL`, `CONSUMED`

**Optional Frontmatter:**
```yaml
session_id: "2025-12-05-001"  # Session identifier
prior_handoff: null           # Previous handoff in chain
next_steps: []                # Action items
blocking: []                  # Blockers identified
```

---

#### I - Index

**Purpose:** Navigation documents linking to other docs.

**Characteristics:**
- Primarily links and brief descriptions
- Provides structure to a directory
- May include status summaries

**Naming:** `README.md` or `INDEX.md`
- Standard for directory roots

**Typical Status:** `PUBLISHED` (usually implicit)

**Optional Frontmatter:**
```yaml
scope: "boot-subsystem"       # What this index covers
```

---

## Document Status

Status is orthogonal to type. Any document can have any status (though some combinations are more common).

### Status Values

| Status | Meaning | Typical Use |
|--------|---------|-------------|
| `AUTHORITATIVE` | Source of truth, actively maintained | Specs, References |
| `PUBLISHED` | Complete and released | Guides |
| `PROPOSED` | Under consideration | Proposals |
| `ACCEPTED` | Proposal accepted, pending implementation | Proposals |
| `REJECTED` | Proposal rejected | Proposals |
| `DRAFT` | Work in progress | Drafts, early Specs |
| `WIP` | Active development | Any |
| `INFORMAL` | Not authoritative, reference only | Notes |
| `CONTEXTUAL` | Session-specific context | Handoffs |
| `CONSUMED` | Handoff has been used | Handoffs |
| `OUTDATED` | Superseded or stale | Any |
| `DEPRECATED` | Replaced, will be removed | Any |
| `ARCHIVED` | Historical reference only | Any |

### Status Lifecycle

```
             ┌─────────────────────────────────────────┐
             │                                         │
             ▼                                         │
          ┌──────┐                                     │
          │ DRAFT │ ──────────────────────────────┐    │
          └──┬───┘                                │    │
             │                                    │    │
     ┌───────┴────────┐                           │    │
     ▼                ▼                           │    │
┌──────────┐    ┌──────────┐                      │    │
│ PROPOSED │    │ PUBLISHED │ ◄────────────────┐  │    │
└────┬─────┘    └────┬─────┘                   │  │    │
     │               │                         │  │    │
┌────┴────┐          │                         │  │    │
▼         ▼          │                         │  │    │
ACCEPTED  REJECTED   │                         │  │    │
│                    │                         │  │    │
▼                    │                         │  │    │
┌─────────────────┐  │                         │  │    │
│  AUTHORITATIVE  │ ◄┘                         │  │    │
└────────┬────────┘                            │  │    │
         │                                     │  │    │
         ▼                                     │  │    │
    ┌──────────┐      ┌──────────┐             │  │    │
    │ OUTDATED │ ───► │ DEPRECATED │ ───► ARCHIVED ─┘
    └──────────┘      └──────────┘
```

---

## Required Frontmatter (All Types)

Every document MUST have these base frontmatter fields:

```yaml
---
created: YYYY-MM-DD           # Creation date
modified: YYYY-MM-DD HH:MM:SS # Last modification
status: STATUS_VALUE          # See status values above
type: TYPE_CODE               # S, R, P, D, G, N, H, or I
tags:                         # Optional tags array
---
```

### Optional Base Fields

These fields are available to all types:

```yaml
sensitivity: public           # public, internal, confidential
accessed: YYYY-MM-DD HH:MM:SS # Last access (auto-managed)
author: "name"                # Primary author
reviewers: []                 # Who reviewed this
related: []                   # Related documents
```

---

## Filename Validation Rules

| Type | Pattern | Valid Examples | Invalid Examples |
|------|---------|----------------|------------------|
| S | `*-spec.md` | `BOOT-FSM-spec.md` | `boot-spec.md` (no subject) |
| R | `*-ref.md` or `*-reference.md` | `CR0-ref.md` | `reference.md` (no subject) |
| P | `*-prop.md` | `BIOS-interface-prop.md` | `proposal.md` |
| D | `DRAFT-*.md` | `DRAFT-new-feature.md` | `draft-feature.md` (lowercase) |
| G | `*-guide.md` | `hardware-debug-guide.md` | `how-to-debug.md` |
| N | (free) | `notes.md`, `meeting.md` | N/A |
| H | `*-handoff.md` | `session-handoff.md` | `handoff.md` (no subject) |
| I | `README.md` or `INDEX.md` | `README.md` | `index.md` (case) |

---

## Migration Guide

### From DPD-001 to UPD-001 Conventions

| Old Pattern | Old `status:` | New Type | New `status:` |
|-------------|---------------|----------|---------------|
| `*-spec.md` with `AUTHORITATIVE` | AUTHORITATIVE | S | AUTHORITATIVE |
| `*-prop.md` with `PROPOSED` | PROPOSED | P | PROPOSED |
| `DRAFT-*.md` | DRAFT | D | DRAFT |
| How-to docs (no suffix) | (none) | G | PUBLISHED |
| `README.md` | (none) | I | PUBLISHED |
| Notes | (none) | N | INFORMAL |

### Recommended Actions

1. Add `type:` field to existing documents
2. Rename files to match new naming conventions
3. Review `status:` values against new definitions
4. Add type-specific optional fields as appropriate

---

## Templater Integration

The Templater system uses this taxonomy:

1. **Type Selection**: User selects type when creating new document
2. **Naming Enforcement**: Template suggests/enforces correct filename
3. **Status Default**: Template sets sensible default status per type
4. **Optional Fields**: Template includes type-specific optional fields

See `Templates/` for implementation.

---

## See Also

- [base.md](Templates/base.md) - Base template
- [Templates/](Templates/) - Type-specific templates
- [README.md](README.md) - Templater configuration guide

---

**Last Updated:** 2025-12-05
**Maintainer:** UPD-001 Documentation Team
