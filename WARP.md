# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Repository Overview

**UPD-001** is an Obsidian-based documentation vault for hardware design projects, primarily focused on VHDL component documentation and technical notes. The repository integrates with **Obsidian Publish** and **GitHub** for multi-platform access.

- **GitHub**: https://github.com/sealablab/UPD-001
- **Obsidian Publish**: https://publish.obsidian.md/upd-001/

## Repository Structure

```
UPD-001/
├── docs/               # Primary documentation (markdown notes)
│   ├── N/             # Notes and meta-documentation
│   └── *.md           # Standalone documentation files
├── Templater/         # Obsidian Templater plugin files
│   ├── Templates/     # Active template files
│   │   ├── user_scripts/    # JavaScript modules for templates
│   │   ├── base.md          # Standard document template
│   │   └── new_vhdl_component.md  # Master VHDL pair creator
│   └── Templater_docs/      # Template system documentation
├── DPD_B/             # Backend design files (currently empty)
├── DPD_F/             # Frontend design files (currently empty)
└── .obsidian/         # Obsidian configuration and plugins
```

## Architecture Patterns

### 1. Dual-Format VHDL Documentation System

The repository implements a **paired file system** for VHDL components:
- **`.vhd`** files contain VHDL source code with comment-based frontmatter
- **`.vhd.md`** files contain documentation with YAML frontmatter
- Files are **bidirectionally linked** using relative paths
- Frontmatter fields are **compatible** between both formats (trivially mappable)

**Key Design Principles:**
- Works inside Obsidian but doesn't depend on it
- Compatible with plain text editors (vi, VS Code, etc.)
- Obsidian automatically tracks file renames/moves via native markdown links
- Uses underscores (not hyphens) in filenames for VHDL build system compatibility

**Frontmatter Schema (Shared Fields):**
- `file`: Filename
- `author`: Author name (default: "jellch")
- `created`: Creation date (YYYY-MM-DD)
- `modified`: Last modification timestamp (YYYY-MM-DD HH:MM:SS)
- `doc` (`.vhd` only): Link to paired `.vhd.md` file
- `code` (`.vhd.md` only): Link to paired `.vhd` file
- `accessed` (`.vhd.md` only): Last access timestamp (Obsidian-managed)

### 2. Obsidian Templater Integration

The repository uses **Obsidian Templater** plugin with custom JavaScript modules:

**User Scripts** (`Templater/Templates/user_scripts/`):
- `templater_times.js` - Manages frontmatter time fields (created/modified/accessed)
- `templater_vhdl_pair.js` - Handles VHDL file pair creation and linking
- `templater_internal_module.js` - Internal utilities

**Startup Hook:**
- `00_times_startup_hook.md` runs automatically on Obsidian startup
- Updates time tracking for active files

**Configuration:**
- Template folder: `Templater/Templates/`
- User scripts folder: `Templater/Templates/user_scripts/`
- Enable folder templates: Yes
- Enable system commands: No

### 3. Documentation Publishing System

Documents include publishing links in their frontmatter/footer:
- **Obsidian Publish**: `https://publish.obsidian.md/upd-001/[path]`
- **GitHub**: `https://github.com/sealablab/UPD-001/blob/main/[path]`
- **GitHub Edit**: `https://github.com/sealablab/UPD-001/edit/main/[path]`

### 4. File Organization

**Ignored Files** (`.obsidian/app.json`):
- Build artifacts: `*.o`, `*.cf`, `work-obj*.cf`, `sim_build`, `*.vcd`, `*.ghw`, `*.fst`, `results.xml`
- Python artifacts: `__pycache__`, `*.pyc`, `*.pyo`, `.venv`, `venv`
- IDE/editor: `.vscode`, `.idea`, `*.swp`, `*.swo`
- System: `.git`, `.DS_Store`, `*.log`
- Directories: `notes`, `scratch`, `moku_backup_*`

## Common Commands

### Git Operations

```bash
# View changes (standard git workflow)
git status
git diff
git add <file>
git commit -m "message"
git push origin main

# View repository info
git remote -v
git log --oneline -20
```

### File Management

```bash
# Find VHDL files
find . -name "*.vhd" -o -name "*.vhd.md" | grep -v ".git"

# Find markdown documentation
find docs -type f -name "*.md"

# List template files
find Templater/Templates -type f
```

### Obsidian-Specific Workflows

**Creating VHDL Component Pairs:**
1. Open Obsidian Command Palette: `CMD-P`
2. Run: `Templater: Create new note from template`
3. Select: `new_vhdl_component`
4. Provide:
   - Directory (e.g., `rtl`, `components/utils`)
   - Component name (hyphens converted to underscores)
   - Author name (default: `jellch`)

**Creating Standard Documentation:**
1. Create new markdown file in `docs/`
2. Apply template: `base.md` from Templater
3. Template auto-populates frontmatter and publishing links

## Development Guidelines

### Naming Conventions

- **VHDL files**: Use underscores as delimiters (e.g., `clk_divider.vhd`, not `clk-divider.vhd`)
- **Documentation**: Use descriptive names with underscores or hyphens
- **Paired files**: Base name must match between `.vhd` and `.vhd.md`

### Working with VHDL Files

- Always maintain paired `.vhd` and `.vhd.md` files
- Use the master template (`new_vhdl_component.md`) for new components
- Keep frontmatter synchronized between paired files
- Use relative paths for links to ensure portability

### Working with Templates

- Templates are in `Templater/Templates/`
- User scripts are in `Templater/Templates/user_scripts/`
- Documentation for templates is in `Templater/Templater_docs/`
- Debug templates are in `Templater/Templates/times_debug/` (for development)

### Frontmatter Management

All markdown files should include frontmatter:
```yaml
---
created: YYYY-MM-DD
modified: YYYY-MM-DD HH:MM:SS
accessed: YYYY-MM-DD HH:MM:SS
type: [documentation|note|component]
tags: []
---
```

The `templater_times.js` script automatically manages time fields when files are created or modified in Obsidian.

## Important Notes

- **Do not commit** `.obsidian/workspace.json` changes casually (contains user-specific window state)
- **Trash location**: Local (`.trash/`) - excluded from version control via userIgnoreFilters
- **Link format**: Always use markdown links with relative paths, not wiki-style `[[links]]`
- **New files**: Default location is current directory
- **Link updates**: Enabled (`alwaysUpdateLinks: true`) - Obsidian auto-updates links on rename/move

## Plugin Dependencies

Required Obsidian plugins:
- **Templater** - Core template system with user scripts
- **Mermaid Tools** - Diagram support
- **Excalidraw** - Drawing integration
- **Leader Hotkeys** - Keyboard shortcuts
- **Minimal Settings** - Theme customization

## Repository Metadata

- **Project**: UPD-001 (Unified Project Documentation)
- **Organization**: sealablab
- **License**: See LICENSE file
- **Primary Tool**: Obsidian (knowledge base)
- **Version Control**: Git (GitHub)
