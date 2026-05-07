---
name: promote-to-b03
description: Move a mature A01 note to B03 Resources with a promotion checklist
invocation: "promote <note> to b03" | "/promote-to-b03 <note>"
---

# Skill: promote-to-b03

## When to use

A note sat in `A01 Processing` long enough to mature. It:
- Has a clear title
- Has a defined central idea
- Has permanent value (not a task, not ephemeral)
- You will reference it again

## What I do

1. **Locate the note** in `A01 Processing/` (partial match on filename allowed)
2. **Run the promotion checklist** and report each item:
   - [ ] Has a clear, scannable title?
   - [ ] Central idea defined in the first paragraph?
   - [ ] At least one wiki-link to another note?
   - [ ] No unresolved TODOs or placeholders left?
   - [ ] Filename is kebab-case?
3. **Flag issues** if any check fails (I don't auto-fix — you decide)
4. **Propose a destination** inside `B03 Resources/` based on content:
   - Technical → `studies/Development/<area>/`
   - Productivity → `studies/productivity/`
   - Finance → `investment/`
   - Standalone → `B03 Resources/<name>.md`
5. **Ask for confirmation** on destination
6. **Suggest related anchors** in A02 that might link to this note
7. After approval: **execute the `mv`** and update any existing wiki-links

## Rules

- **I don't edit content.** If the checklist fails, I report; you edit.
- **I don't invent links.** Anchor/backlink suggestions come only from existing notes.
- **Reversible move.** The original path is logged in `C04/03 Logs & Memory/promotions.md`.

## Variations

- `/promote-to-b03 <note>` — full promotion with checklist
- `/promote-to-b03 <note> --skip-checklist` — move without validation (trust mode)
- `/promote-to-b03 <note> --dry-run` — report destination only, don't move

## Interaction example

```
you: /promote-to-b03 zettelkasten-notes
me:  Found: A01 Processing/zettelkasten-notes.md
     Checklist:
     ✅ Clear title
     ✅ Central idea defined
     ⚠️  No wiki-links yet
     ✅ No TODOs
     ✅ kebab-case
     
     Proposed destination: B03 Resources/studies/productivity/zettelkasten-notes.md
     
     Candidate anchor: [[Second Brain]] (not yet created)
     
     Proceed? (y / change destination / abort)
```
