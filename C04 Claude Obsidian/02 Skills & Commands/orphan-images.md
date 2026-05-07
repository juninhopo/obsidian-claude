---
name: orphan-images
description: Find images in A03 Attachments that no note references
invocation: "find orphan images" | "/orphan-images"
---

# Skill: orphan-images

## When to use

- Monthly cleanup
- After a big reorganization
- When `A03 Attachments/` feels bloated

## What I do

1. **List all image/attachment files** in `A03 Attachments/`:
   - Extensions: `.png`, `.jpg`, `.jpeg`, `.gif`, `.webp`, `.pdf`, `.svg`
2. **For each file**, grep the vault for references:
   - `![[filename.ext]]` (Obsidian embed)
   - `[[filename]]` (wiki-link without extension)
   - `![alt](path/filename.ext)` (markdown image)
   - `](filename.ext)` (markdown link)
3. **Build two lists:**
   - ✅ **Referenced** — appears in at least one note
   - 🔴 **Orphan** — no references found
4. **Report** orphans in a table with filename, size, and last-modified date:

   ```
   === ORPHAN IMAGES (N files, X MB total) ===
   1. Screenshot 2024-03-15 at 10.22.png  (2.4 MB, 2 years ago)
   2. old-diagram.png                     (180 KB, 1 year ago)
   ...
   ```
5. **Do NOT delete automatically.** Wait for your decision per file:
   - `delete` — remove (goes to Google Drive trash, 30-day recovery)
   - `archive` — move to `B04 Archives/orphan-images/`
   - `keep` — leave in A03 (maybe you plan to use it)

## Rules

- **No deletion without per-file confirmation.**
- **Bulk moves allowed** if you explicitly say "archive all" or "delete all orphans from 2024".
- **Log every action** in `C04/03 Logs & Memory/orphan-images-YYYY-MM-DD.md`.

## Variations

- `/orphan-images` — full report, await decisions
- `/orphan-images list` — report only, no prompts
- `/orphan-images archive-all` — move everything found straight to `B04 Archives/orphan-images/` (still asks for final confirmation)
- `/orphan-images --older-than 6m` — only report orphans older than 6 months

## False-positive notes

- Files matched by `[[filename]]` (without extension) are treated as referenced
- Renamed images in notes become false orphans — I surface them with a 🔄 flag if the filename is similar to a referenced one

## Interaction example

```
you: /orphan-images --older-than 1y
me:  Scanned A03 Attachments — 412 files total
     Referenced: 378
     Orphans older than 1 year: 34 files (48 MB)
     
     Top by size:
     1. Screenshot 2023-11-02 at 14.30.png (12.1 MB)
     2. old-architecture-v1.pdf            (8.2 MB)
     ...
     
     Full list in C04/03 Logs & Memory/orphan-images-2026-04-23.md
     
     What do you want to do? (archive-all / delete-all / case-by-case)
```
