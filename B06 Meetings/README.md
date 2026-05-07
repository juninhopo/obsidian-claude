# B06 Meetings

> **Memory of meetings — transcripts and notes.**

## What goes here

Every meeting you attend becomes a markdown note. Primary source: Granola (transcript + notes). Secondary sources: Google Meet, Zoom, manual notes.

## Naming convention

- File: `YYYY-MM-DD-<slug>.md`
- Slug: kebab-case, short, descriptive (`weekly`, `darlan-eric`, `disaster-recovery`)
- Multiple meetings of the same type on the same day → numeric suffix `-2`, `-3`

Examples:
- `2026-04-27-weekly.md`
- `2026-04-15-darlan-paulo.md`
- `2026-03-28-disaster-recovery.md`

## Folder structure

```
B06 Meetings/
├── README.md
├── _meetings.md              ← index/dashboard (status, upcoming, open action items)
├── 2026/
│   ├── 03/
│   │   ├── 2026-03-19-...md
│   │   └── ...
│   └── 04/
│       └── ...
└── 2027/
```

Year/month organization keeps the file picker light even with 200+ meetings/year.

## Required frontmatter

```yaml
---
date: 2026-04-27
type: weekly | daily | 1on1 | external | onboarding | retro | other
project: blindpay              # or null
participants: [Darlan, Eric]
source: granola | manual | zoom | meet
granola-id: <uuid>             # if source = granola
status: raw | processed | actioned
---
```

- `status: raw` — just imported, not yet reviewed
- `status: processed` — you read it, marked decisions and action items
- `status: actioned` — every action item has been moved to its `_<project>.md`
- `status: empty` — no notes and no transcript in the source (Granola registered the event but didn't capture content). Placeholder only.

## Template

`C04 Claude Obsidian/06 Templates & References/template-meeting.md`

## Capture flow

**Manual (post-meeting):**
1. Copy notes from Granola
2. Create file following naming convention
3. Paste into template
4. Mark `status: raw`
5. When processing: link with `[[<project>]]` and move action items into `_<project>.md`

**Bulk import (Granola → vault):**
- Skill: `/process-meetings`
- Reads `cache-v6.json` from `~/Library/Application Support/Granola/`
- Converts ProseMirror JSON → markdown
- Creates one file per meeting, populates frontmatter
- Dedupes by `granola-id`

## Rule

**Authorship of raw notes = Granola/transcript.** You (and I, during import) can transcribe verbatim from Granola — this does not violate the authorship rule because the source is the recorded meeting, not invented by me.

**The summary, decisions and action items YOU add** follow the normal authorship rule (B03). I do not write synthesis without review.

**Verbatim rule applies** when moving action items from here into `_<project>.md`: preserve text exactly.

## Maintenance

- [ ] Weekly: process raw meetings from the week
- [ ] Monthly: run `/weekly-review` covering open action items
- [ ] Quarterly: archive `actioned` meetings older than 90 days? (decide)

## Cross-references

- Action items go to `_<project>.md` of the corresponding project in `B01 Projects/`
- Major architectural decisions → also go to `C04 Claude Obsidian/07 Decisions & History/`
- New terms learned in meetings → propose addition to `B01 Projects/<project>/CLAUDE.md` (Glossary/People sections)
