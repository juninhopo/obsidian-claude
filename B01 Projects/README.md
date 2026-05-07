# B01 Projects

> **Projects. Active and archived live together here.**

## What goes here

Each project is a folder. Inside, everything related:
- Tasks (`_<project>.md` as index)
- Technical notes
- Briefings
- Architectural decisions

## Naming convention

- Folder name: kebab-case, lowercase, no accents
- Project name = brand name (e.g., `acme`, `widget-v2`)
- Project index file: `_<project>.md` (underscore prefix so it appears at the top)

## Active vs. Inactive

We do not use active/inactive subfolders — all live at the same level. To mark status:

**Option 1 (tag in the index file):**
```markdown
---
status: active
---
```

**Option 2 (emoji prefix in the index):**
- `🟢 active`
- `⚫ inactive`
- `🔴 blocked`

Your call. Important: the **index file** (`_<project>.md`) is the source of truth for status.

## Current projects

*List your active and inactive projects here. Example:*

**Active:** project-1, project-2
**Inactive/legacy:** old-project-1, old-project-2

## When to create a project

- Has a clear objective with start/end
- Will last more than 1 week
- Generates at least 3 notes

Less than that → goes to A05 Backlog or becomes a single note in B03.

## Promote from A01/A05

`/create-project <name>` — creates folder with structure based on `C04/06/template-project.md`
