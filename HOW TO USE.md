# How to use this vault

> A second brain that **multiplies your thinking** instead of accumulating notes.
> Works in Obsidian, operated by Claude Code via custom skills.

---

## 🎯 What this is

A note-taking system built on three principles:

1. **You are the author.** Permanent thinking lives in your hands. The AI never writes a thesis or edits authored content without explicit review.
2. **Memory is commodity, utility is the moat.** The value is not the pile of notes — it's the workflows on top.
3. **The vault doesn't get smarter on its own.** Active maintenance is the price of compounding.

The structure follows **PARA + Zettelkasten + MOCs**, with a third layer for AI tooling.

---

## 🚀 Quick start (5 minutes)

If this is your first time:

1. **Configure Obsidian:**
   - `Settings → Files & Links → Default location for new attachments` → `In the folder specified below` → path: `A03 Attachments`
   - `Settings → Files & Links → Automatically update internal links` → ON
   - `Settings → Files & Links → New link format` → `Shortest path when possible`
   - `Settings → Core Plugins → Daily Notes` → ON, point template to `C04 Claude Obsidian/06 Templates & References/template-daily-note`

2. **Read the contract:** `C04 Claude Obsidian/CLAUDE.md` — what the AI can and can't do.

3. **Run your first command:** `/create-daily` (creates today's daily note from template).

4. **Capture something:** drop any note in `A00 Inbox/` and run `/process-inbox`.

---

## 🗺️ Mental model — A / B / C

```
A — daily flow (capture and processing)
  A00 Inbox             unclassified entry point
  A01 Processing        ideas being refined
  A02 Anchor Topics     MOCs by theme (your thinking)
  A03 Attachments       images, PDFs (centralized)
  A04 Daily Notes       daily journal
  A05 Backlog           parking lot, no date

B — archive (projects, knowledge, closed)
  B01 Projects          active and inactive
  B03 Resources         studies, references, authored notes
  B04 Archives          dead but preserved
  B05 Systems           SOPs, workflows you execute

C — tools and agents (integrations)
  C02 Readwise          highlights from reading
  C03 Books             book catalog (Dataview-friendly)
  C04 Claude Obsidian   AI workspace (skills, templates, logs)
  C05 Excalidraw        all drawings centralized
```

Each folder has a `README.md` explaining purpose and rules. **Read it before working in a folder.**

---

## 🔄 The Loop (Flow 3)

```
YOU think and write (Flow 1) ──┐
                               ├──> LOOP ──> deliverables
AI processes and executes (Flow 2) ──┘         (yours + mine)
```

1. **Input** — your experiences, studies, projects → land in A00/A04
2. **Transformation** — you write (A02/B03), the AI processes (C04)
3. **Output** — consolidated knowledge (yours) + practical artifacts (AI's)
4. **Feedback** — you review, AI updates context for next pass

---

## 📅 Daily workflow

**Morning:**
- `/create-daily` — opens today's note with yesterday's pending tasks pulled in
- Set the day's focus (one sentence)

**During the day:**
- Capture anything → `A00 Inbox/` (raw) or directly into the daily
- New project? → `/create-project <name>`
- Need an MOC for a recurring theme? → `/create-anchor <topic>`

**End of day:**
- Quick review section in the daily — was the focus met? Any idea worth promoting?

---

## ⌨️ Commands

All commands run inside Claude Code. Three ways to invoke:

1. **Slash command:** `/process-inbox`
2. **Natural language:** "process my inbox", "create anchor Observability"
3. **Looped automation:** `/loop 7d /weekly-review`

Full skill definitions live in `C04 Claude Obsidian/02 Skills & Commands/`.

---

### 📥 Daily / Capture

#### `/create-daily`
Creates today's daily note from `template-daily-note`. Skips if file exists. Pulls unfinished tasks from yesterday.

- `/create-daily` — today
- `/create-daily tomorrow` — tomorrow (planning ahead)
- `/create-daily 2026-04-25` — specific date

#### `/process-inbox`
Triage `A00 Inbox`. Reads each item, proposes destination (A01/A02/B01/B03/A05/B04 or discard), waits for approval before moving.

- `/process-inbox` — full mode
- `/process-inbox quick` — move only obvious ones
- `/process-inbox full` — propose destination for every item

---

### 🗺️ Knowledge / Structure

#### `/create-anchor <topic>`
Creates an MOC in `A02 Anchor Topics/<topic>.md`. Searches the vault for related notes and fills the child list. **Thesis stays blank — you write it.** Initializes the `<!-- anchor-meta -->` block (search terms + exclusions).

- `/create-anchor Observability`
- `/create-anchor Payments --from-backlog` — promotes an A01/A05 note

#### `/refresh-anchor <topic>`
Audits an anchor against the current vault — surfaces new notes to link, broken links to fix. **Authored sections never touched.** Persistent rejections via the `anchor-meta` block.

- `/refresh-anchor <topic>` — single anchor
- `/refresh-anchor --all` — every anchor in A02
- `/refresh-anchor --stale 3m` — only anchors not touched in 3 months
- `/refresh-anchor <topic> --dry-run` — report only

#### `/promote-to-b03 <note>`
Moves a mature A01 note to `B03 Resources` with a checklist (title, central idea, links, no TODOs, kebab-case). Proposes destination subfolder.

- `/promote-to-b03 <note>` — full checklist
- `/promote-to-b03 <note> --skip-checklist` — trust mode
- `/promote-to-b03 <note> --dry-run` — report only

#### `/link-suggestions <note>`
Proposes wiki-links for a note based on content overlap. Ranked by confidence. **No auto-insert.**

- `/link-suggestions <note>`
- `/link-suggestions <note> --limit 5`
- `/link-suggestions <note> --anchors-only`
- `/link-suggestions <note> --from <folder>`

---

### 📁 Projects / Archive

#### `/create-project <name>`
Scaffolds a new project in `B01 Projects/<name>/` with subfolders (notes, decisions, specs, daily) and `_<name>.md` from template. Collision-checked.

- `/create-project <name>` — full scaffold
- `/create-project <name> --minimal` — index file only
- `/create-project <name> --from <source-note>` — promote A01/A05 into a project
- `/create-project <name> --link-anchor <anchor>` — pre-link to an anchor

#### `/archive-project <project>`
Moves a finished/abandoned project from `B01` to `B04 Archives/old-projects/`. Appends a post-mortem scaffold (fields blank — yours to fill) and sets `status: archived`.

- `/archive-project <project>`
- `/archive-project <project> --no-post-mortem`
- `/archive-project <project> --dry-run`

#### `/project-recap <name>`
Generates a weekly recap for a project — done, in-progress, blocked, plus literal task tree per day extracted from daily notes. **Verbatim** — no summarizing of your tasks. Output ready for standup or weekly meeting.

- `/project-recap <name>` — last 7 days, markdown format
- `/project-recap <name> --days 14` — 2 weeks
- `/project-recap <name> --since 2026-04-20` — explicit start date
- `/project-recap <name> --format slack` — bullet-style for messaging apps
- `/project-recap <name> --save` — save report to `B05 Systems/recaps/<project>/YYYY-MM-DD.md`
- `/project-recap --all` — recap every project with activity in window

---

### 🔧 Maintenance

#### `/weekly-review`
Vault health check. Scans A00 age, A01 stagnation, B01 project updates, broken links, duplicates. Report lands in `B05 Systems/weekly-reviews/YYYY-MM-DD.md`.

- `/weekly-review` — standard
- `/weekly-review light` — flags only
- `/weekly-review deep` — with link analysis + duplicates

#### `/orphan-images`
Lists images in `A03 Attachments/` not referenced by any note. **No auto-delete** — you decide per file or in bulk.

- `/orphan-images` — full report, await decisions
- `/orphan-images list` — report only
- `/orphan-images archive-all` — move orphans to `B04 Archives/orphan-images/`
- `/orphan-images --older-than 6m`

#### `/sync-shared`
Syncs the build (skills, templates, READMEs, CLAUDE.md, HOW TO USE) into the `_shared/` kit at vault root. Genericizes the 3 READMEs that mention personal content. Diff-first, apply-second.

- `/sync-shared` — full sync with per-file approval
- `/sync-shared --dry-run` — show diff plan, apply nothing
- `/sync-shared --force` — apply all changes without per-file approval
- `/sync-shared --only <file>` — sync just one canonical file

---

### 📚 Search

#### `/search-books <query>`
Queries the `C03 Books` catalog by title, author, tag, status, or rating.

- `/search-books <query>` — free text
- `/search-books --status finished --rating >=4` — best books I've read
- `/search-books --tag productivity` — by tag
- `/search-books --author "James Clear"` — by author
- `/search-books --want-to-read` — shortcut

---

## 📄 Templates

In `C04 Claude Obsidian/06 Templates & References/`:

| Template | Used by | Purpose |
|----------|---------|---------|
| `template-daily-note` | `/create-daily` | Daily journal scaffold |
| `template-anchor-topic` | `/create-anchor` | MOC structure with anchor-meta block |
| `template-inbox-item` | manual | Quick-capture format |
| `template-project` | `/create-project` | Project index file scaffold |
| `template-project-claude` | `/create-project` | Per-project CLAUDE.md (context, glossary, patterns) |

---

## 🧠 CLAUDE.md hierarchy (how the AI loads context)

Three layers govern AI behavior. The global file is just a pointer — everything lives in the vault.

| Layer | Path | Purpose |
|-------|------|---------|
| Global pointer | `~/.claude/CLAUDE.md` | One sentence: "read the vault CLAUDE.md". Nothing else. |
| Vault rules | `C04 Claude Obsidian/CLAUDE.md` | All behavioral rules (librarian, verbatim, pattern capture, project routing) |
| Per-project | `B01 Projects/<project>/CLAUDE.md` | Conventions, glossary, people, decisions, aliases for that project |

**Cross-context:** When you mention a project (e.g., "blindpay") even outside the vault (e.g., while coding in `~/Developer/blindpay/blindpay-v2/`), the AI reads the project's CLAUDE.md from the vault. Project context follows you anywhere.

**Pattern capture:** When you describe a project-specific convention or pattern, the AI proposes saving it to that project's CLAUDE.md. Vault gets smarter every conversation.

---

## ⏰ Periodic workflows

| Cadence | Workflow | How |
|---------|----------|-----|
| Daily, morning | `/create-daily` | Manual or `/loop 1d /create-daily` or Obsidian Daily Notes plugin |
| Weekly, Monday | `/project-recap <name> --save` (standup prep) | Manual or `/loop 7d /project-recap <name> --save` |
| Weekly, Friday | `/process-inbox` + `/weekly-review` | Manual or `/loop 7d` |
| Monthly | `/orphan-images list` | Manual or `/loop 30d` |
| Quarterly | `/refresh-anchor --all --stale 3m` | Manual or `/loop 90d` |
| As needed | `/create-anchor`, `/promote-to-b03`, `/create-project`, `/archive-project`, `/link-suggestions`, `/search-books` | Manual |

---

## ⚙️ Obsidian configuration

**Required:**
- Files & Links → Default attachment location: `A03 Attachments`
- Files & Links → Automatically update internal links: ON
- Files & Links → New link format: Shortest path when possible
- Files & Links → Use [[Wikilinks]]: ON

**Recommended core plugins:**
- Daily Notes → folder `A04 Daily Notes`, template from `C04/06`
- Templates → folder `C04 Claude Obsidian/06 Templates & References`

**Recommended community plugins:**
- Excalidraw → save in `C05 Excalidraw/`
- Readwise Official → sync to `C02 Readwise/`
- Dataview → enables queries (e.g., on `C03 Books`)

---

## ⚠️ Anti-patterns

> "Your vault doesn't get smarter on its own."

Without active maintenance, the system degrades into:
- Duplicate notes
- Contradictions between old and new entries
- Wiki nobody maintains
- Inbox graveyard

**Required maintenance:**
- Periodic A00 review (weekly) — process or archive
- A05 Backlog pruning (quarterly)
- Anchor refreshes (quarterly)
- Update CLAUDE.md when rules change

**Don't:**
- Dump highlights from C02 directly into B03 — distill them in your own words
- Create anchors with no thesis (they decay into link salads)
- Let a project sit in B01 with no update for 60+ days without flagging status

---

## 📖 Where to learn more

| File | What's there |
|------|--------------|
| `C04 Claude Obsidian/CLAUDE.md` | The contract — what the AI can/can't do |
| `C04 Claude Obsidian/02 Skills & Commands/` | Full skill definitions (the source of truth) |
| `C04 Claude Obsidian/06 Templates & References/` | All note templates |
| Each folder's `README.md` | Purpose, flow, and rules for that folder |
| `C04 Claude Obsidian/03 Logs & Memory/` | History of moves, promotions, weekly reviews |

---

## 🆘 Troubleshooting

| Symptom | Probable cause | Fix |
|---------|----------------|-----|
| New screenshots go to wrong folder | Files & Links setting wrong | Reset attachment path to `A03 Attachments` |
| `[[wiki-link]]` shows red | Target file doesn't exist | Create it, or fix the link |
| Skill behaves differently than docs | Skill file edited locally | Re-read the skill file in `C04/02` |
| Inbox keeps overflowing | Skipping `/process-inbox` | Schedule `/loop 7d /process-inbox` |
| Anchor feels stale | Has not been refreshed | `/refresh-anchor <topic>` |

---

*Last updated: 2026-04-27*
