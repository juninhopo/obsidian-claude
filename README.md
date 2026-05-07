# obsidian-claude

An Obsidian vault operating system built for [Claude Code](https://claude.ai/code).

A structured second brain with a full set of AI skills, note templates, and folder conventions. Drop it into a new vault, configure two Obsidian settings, and you have a working system — daily notes, project tracking, meeting processing, inbox triage, and more.

---

## What's inside

```
A00 Inbox/              unclassified entry point
A01 Processing/         ideas being refined
A02 Anchor Topics/      MOCs by theme (your thinking)
A03 Attachments/        images, PDFs (centralized)
A04 Daily Notes/        daily journal
A05 Backlog/            parking lot, no date

B01 Projects/           active projects
B03 Resources/          studies, references, authored notes
B04 Archives/           dead but preserved
B05 Systems/            SOPs, workflows you execute
B06 Meetings/           meeting notes and transcripts
B07 Ralph/              local AI agent config

C04 Claude Obsidian/
├── CLAUDE.md           the AI contract (librarian, not author)
├── 02 Skills & Commands/   12 custom skills for Claude Code
└── 06 Templates & References/  note templates

HOW TO USE.md           full guide — start here
```

---

## Quick start

**1. Clone and open in Obsidian**

```bash
git clone git@github.com:juninhopo/obsidian-claude.git my-vault
```

Open the folder as an Obsidian vault.

**2. Configure two Obsidian settings**

- `Settings → Files & Links → Default location for new attachments` → `In the folder specified below` → `A03 Attachments`
- `Settings → Core Plugins → Daily Notes` → ON → template: `C04 Claude Obsidian/06 Templates & References/template-daily-note`

**3. Set up Claude Code**

Add this to your `~/.claude/CLAUDE.md` (create if it doesn't exist):

```
Read this file before acting in this vault:
/absolute/path/to/your/vault/C04 Claude Obsidian/CLAUDE.md
```

Replace the path with the absolute path to your vault.

**4. Run your first command**

Open Claude Code inside the vault folder and run:

```
/create-daily
```

Today's daily note is created with the correct structure.

---

## Skills

All skills live in `C04 Claude Obsidian/02 Skills & Commands/`. Invoke them in Claude Code:

| Skill | What it does |
|-------|-------------|
| `/create-daily` | Creates today's daily note, carries over unfinished tasks, pulls Google Calendar events |
| `/process-inbox` | Triages `A00 Inbox` — proposes destination for each item, waits for approval |
| `/process-meetings` | Imports meetings from Granola into `B06 Meetings/` with AI summary |
| `/create-project <name>` | Scaffolds a new project with subfolders and index file |
| `/project-recap <name>` | Generates a weekly recap from daily notes — verbatim task tree, ready for standups |
| `/create-anchor <topic>` | Creates a Map of Content (MOC) in `A02 Anchor Topics/` |
| `/weekly-review` | Vault health check — inbox age, stale projects, broken links |
| `/archive-project <name>` | Moves a finished project to `B04 Archives/` with post-mortem scaffold |
| `/promote-to-b03 <note>` | Promotes a mature note from A01 to B03 Resources |
| `/link-suggestions <note>` | Proposes wiki-links based on content overlap |
| `/orphan-images` | Lists unused images in `A03 Attachments/` |
| `/sync-shared` | Syncs build files back to `_shared/` for sharing |

---

## Core principle

**You are the author. The AI is the librarian.**

The AI finds, connects, organizes, and proposes — but never writes permanent notes on your behalf, never edits authored content without review, and never rewrites your text when moving it.

Defined in `C04 Claude Obsidian/CLAUDE.md`. Read it — it's the contract between you and the AI.

---

## Full documentation

→ [`HOW TO USE.md`](HOW%20TO%20USE.md) — complete guide with all commands, templates, workflows, Obsidian config, and troubleshooting.

---

## Requirements

- [Obsidian](https://obsidian.md) (free)
- [Claude Code](https://claude.ai/code) (Claude subscription)
- Optional: [Google Drive](https://drive.google.com) — store the vault inside your Drive folder so it syncs automatically across devices and stays backed up in the cloud
- Optional: [Granola](https://granola.ai) for meeting processing
- Optional: Google Calendar MCP for `/create-daily` to pull events
