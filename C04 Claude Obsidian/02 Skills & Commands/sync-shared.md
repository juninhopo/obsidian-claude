---
name: sync-shared
description: Sync the build (skills, templates, READMEs, CLAUDE.md, HOW TO USE) from the vault into the _shared/ kit, leaving your personal content untouched
invocation: "sync shared" | "/sync-shared"
---

# Skill: sync-shared

## When to use

- After updating any skill, template, README, or CLAUDE.md in the live vault
- Before sharing the `_shared/` kit with someone else
- After running a personalization spree you want NOT to leak into the kit

## What I do

1. **Locate the kit:** `_shared/` at the vault root. If it doesn't exist, abort and suggest the user runs an initial build.
2. **Diff plan** — for each file in the canonical list (below), I check:
   - 🔄 changed (vault has new content, kit is stale) → propose update
   - 🆕 missing in kit → propose copy
   - ❌ removed in vault but still in kit → propose delete
3. **Genericize 3 READMEs** that mention user-specific content:
   - `B01 Projects/README.md` — strip the "Current projects (snapshot)" section, replace with placeholder
   - `B03 Resources/README.md` — strip the user's specific subfolder tree
   - `B04 Archives/README.md` — strip the personal-notes/credentials-private specifics
4. **Show the diff plan** as a report. Wait for approval before applying.
5. After approval: execute copies + deletions. Log each action.
6. **Log** in `C04/03 Logs & Memory/sync-shared-YYYY-MM-DD.md` with the summary of changes.

## Canonical files synced

```
ROOT
├── HOW TO USE.md                                 → _shared/HOW TO USE.md
├── A00 Inbox/README.md                           → _shared/A00 Inbox/README.md
├── A01 Processing/README.md                      → _shared/A01 Processing/README.md
├── A02 Anchor Topics/README.md                   → _shared/A02 Anchor Topics/README.md
├── A03 Attachments/README.md                     → _shared/A03 Attachments/README.md
├── A04 Daily Notes/README.md                     → _shared/A04 Daily Notes/README.md
├── A05 Backlog/README.md                         → _shared/A05 Backlog/README.md
├── B01 Projects/README.md                        → _shared/B01 Projects/README.md  [genericize]
├── B03 Resources/README.md                       → _shared/B03 Resources/README.md  [genericize]
├── B04 Archives/README.md                        → _shared/B04 Archives/README.md  [genericize]
├── B05 Systems/README.md                         → _shared/B05 Systems/README.md
├── B06 Meetings/README.md                        → _shared/B06 Meetings/README.md
├── B07 Ralph/README.md                           → _shared/B07 Ralph/README.md
├── C02 Readwise/README.md                        → _shared/C02 Readwise/README.md
├── C03 Books/README.md                           → _shared/C03 Books/README.md
├── C04 Claude Obsidian/CLAUDE.md                 → _shared/C04 Claude Obsidian/CLAUDE.md
├── C04 Claude Obsidian/02 Skills & Commands/*.md → _shared/.../02 Skills & Commands/
│   (includes calendar-week.md, create-daily.md, process-meetings.md, etc.)
├── C04 Claude Obsidian/06 Templates & References/*.md → _shared/.../06 Templates & References/
└── C05 Excalidraw/README.md                      → _shared/C05 Excalidraw/README.md
```

## Files NEVER touched

- The kit's own top-level `_shared/README.md` — that's authored content explaining the kit (not a clone of the vault)
- Anything inside the kit that wasn't auto-synced (if user added custom files there)
- All user content in the vault (anchors, daily notes, projects, etc.)

## Rules

- **Diff-first, apply-second.** Always show the plan before touching files.
- **Genericization is template-based.** Same patterns each run — predictable diffs.
- **No reverse sync.** I do not pull changes from the kit back into the vault.
- **No content edits.** I copy files. Genericized READMEs follow a fixed transformation rule (documented in this skill).
- **History append-only.** Each run logs a new entry; old logs are not modified.

## Variations

- `/sync-shared` — full sync with diff approval
- `/sync-shared --dry-run` — show diff plan, apply nothing
- `/sync-shared --force` — apply all changes without per-file approval (only if the diff looks trivial — I still show the summary first)
- `/sync-shared --only <file>` — sync just one canonical file

## Genericization rules

### B01 Projects/README.md
Replace the section starting with `## Current projects (snapshot)` and ending before the next `##` with:

```markdown
## Current projects

*List your active and inactive projects here. Example:*

**Active:** project-1, project-2
**Inactive/legacy:** old-project-1, old-project-2
```

### B03 Resources/README.md
Replace the section starting with `## Current structure` and ending before the next `##` with:

```markdown
## Suggested structure

```
B03 Resources/
├── studies/                  (technical and non-technical learning)
├── investment/               (personal finance, optional)
└── <your-domains>/
```

*Adapt to your domains. The folder is loose by design.*
```

### B04 Archives/README.md
Replace the section starting with `## Current structure` and ending before the next `##` with:

```markdown
## Suggested structure

```
B04 Archives/
├── old-projects/             (closed B01 projects)
├── obsolete-resources/       (B03 notes that became false/outdated)
├── daily-notes-archive/      (daily notes older than 6 months, optional)
├── credentials-private/      (sensitive data — isolated, NEVER in public notes)
└── <your-categories>/
```

*Don't actually create empty folders — they're conceptual buckets you fill as needed.*
```

## Interaction example

```
you: /sync-shared
me:  Diff plan for _shared/ (vs vault):
     
     🔄 Changed (3):
       - C04 Claude Obsidian/02 Skills & Commands/refresh-anchor.md
       - C04 Claude Obsidian/CLAUDE.md
       - HOW TO USE.md
     
     🆕 Missing in kit (1):
       - C04 Claude Obsidian/02 Skills & Commands/new-skill.md
     
     ❌ Removed from vault, still in kit (0)
     
     Genericized READMEs: B01, B03, B04 — will re-apply transform from current vault README.
     
     Apply all? (y / per-file / dry-run / abort)
```
