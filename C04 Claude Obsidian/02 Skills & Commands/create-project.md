---
name: create-project
description: Scaffold a new project folder in B01 from template
invocation: "create project <name>" | "/create-project <name>"
---

# Skill: create-project

## When to use

Starting a new project that:
- Has a clear objective with start/end
- Will last more than 1 week
- Will generate at least 3 notes

Less than that → put it in `A05 Backlog` or as a single note in `B03 Resources`.

## What I do

1. **Normalize the project name** to kebab-case, lowercase, no accents
   - "Uma Letra" → `uma-letra`
   - "BlindPay v3" → `blindpay-v3`
2. **Check for collisions:**
   - If `B01 Projects/<name>/` exists → abort, ask if you meant to resume
   - If `B04 Archives/old-projects/<name>/` exists → warn and ask whether to unarchive or use a new name
3. **Create the folder structure:**
   ```
   B01 Projects/<name>/
   ├── _<name>.md       (index file — from template-project.md)
   ├── CLAUDE.md        (project context — from template-project-claude.md)
   ├── notes/           (technical notes)
   ├── decisions/       (ADRs)
   ├── specs/           (PRDs, specs)
   └── daily/           (project-specific daily notes)
   ```
4. **Populate `_<name>.md`** from `C04/06/template-project.md`:
   - Replace `{{projectName}}` → `<name>`
   - Replace `{{date:YYYY-MM-DD}}` → today
   - Leave objective, tasks, decisions **blank** (authorship is yours)
5. **Populate `CLAUDE.md`** from `C04/06/template-project-claude.md`:
   - Replace `{{projectName}}` → `<name>`
   - Replace `{{date:YYYY-MM-DD}}` → today
   - All sections start blank (Domain, People, Conventions, Glossary, Patterns, Aliases, External refs, Decisions)
   - Will be filled organically through pattern capture as you describe the project
6. **Add the project to the active list** in `<vault>/C04 Claude Obsidian/CLAUDE.md` → "Project context loading" section, so the AI routes mentions to the new project's CLAUDE.md
5. **Report back:**
   - "Created B01 Projects/<name>/ with 4 subfolders and _<name>.md"
   - "Fill the Objective and first task when you have a moment"
6. **Offer:** "Want me to add `<name>` to any related A02 anchor?"

## Rules

- **I don't write the objective or tasks.** Authorship is yours.
- **Pre-flight collision check.** Never overwrite an existing project.
- **Subfolders stay empty** until you create content — no placeholder files.

## Variations

- `/create-project <name>` — full scaffold (4 subfolders + index)
- `/create-project <name> --minimal` — only `_<name>.md`, no subfolders
- `/create-project <name> --from <source-note>` — promote an A01/A05 note into a project, move the note to `notes/` inside
- `/create-project <name> --link-anchor <anchor>` — pre-link to an A02 anchor

## Interaction example

```
you: /create-project uma-letra-v2
me:  Checked: B01 Projects/uma-letra-v2/ does not exist.
     Warning: B01 Projects/uma-letra/ exists and is active.
     Is this a v2 of the same project, or a rename? (new / rename / abort)

you: new
me:  Creating:
     B01 Projects/uma-letra-v2/
     ├── _uma-letra-v2.md  (from template-project.md)
     ├── notes/
     ├── decisions/
     ├── specs/
     └── daily/
     
     Done. Fill the Objective in _uma-letra-v2.md when you have a moment.
     
     Relevant anchors: [[Payments]], [[Remote team management]] — want me to cross-link?
```
