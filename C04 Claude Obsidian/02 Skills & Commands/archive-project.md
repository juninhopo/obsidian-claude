---
name: archive-project
description: Move a project from B01 to B04 and generate a post-mortem
invocation: "archive project <project>" | "/archive-project <project>"
---

# Skill: archive-project

## When to use

A project in `B01 Projects/` is done (shipped, cancelled, or abandoned) and should not appear in the active list anymore.

Signs:
- No commits/updates in 60+ days
- You no longer think about it
- Status in the index file flipped to `inactive` or `archived`

## What I do

1. **Locate the project folder** in `B01 Projects/<project>/`
2. **Read the index file** `_<project>.md` to extract:
   - Objective
   - Timeline (start/end)
   - Milestones (which were hit)
   - Architectural decisions
3. **Draft a post-mortem section** appended to the index file, leaving fields **blank** for you:
   ```markdown
   ## Post-mortem (YYYY-MM-DD)
   - Outcome:          *(you fill)*
   - What worked:      *(you fill)*
   - What didn't work: *(you fill)*
   - Learnings:        *(you fill)*
   ```
4. **Update frontmatter** to `status: archived`
5. **Propose the move** to `B04 Archives/old-projects/<project>/`
6. **Ask for confirmation**
7. After approval: **execute the `mv`** and log it in `C04/03 Logs & Memory/archives.md`
8. **Report broken links** — notes elsewhere that referenced `B01 Projects/<project>/...` now point to the new location (wiki-links by filename still work, but full-path markdown links break)

## Rules

- **I don't write the post-mortem content.** I only scaffold. Authorship is yours.
- **I don't delete.** Archive ≠ delete.
- **I check references first.** If other active projects depend on this one, I flag them before moving.

## Variations

- `/archive-project <project>` — standard flow
- `/archive-project <project> --no-post-mortem` — move without post-mortem scaffold
- `/archive-project <project> --dry-run` — report what would happen

## Interaction example

```
you: /archive-project abacatepay
me:  Found: B01 Projects/abacatepay/ (47 notes, 3 subfolders)
     Last update: 2025-12-23
     Status in _abacatepay.md: (not set)
     
     Actions I will take:
     1. Append post-mortem scaffold to _abacatepay.md (fields blank)
     2. Set status: archived in frontmatter
     3. Move folder to B04 Archives/old-projects/abacatepay/
     4. Log the move in C04/03 Logs & Memory/archives.md
     
     4 notes elsewhere reference this project via full-path (markdown-style). Wiki-links are unaffected.
     
     Proceed? (y / skip step / abort)
```
