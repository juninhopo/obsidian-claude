---
name: project-recap
description: Generate a weekly recap for a project — done, in-progress, blocked, plus literal task tree per day from daily notes
invocation: "project recap <name>" | "/project-recap <name>"
---

# Skill: project-recap

## When to use

- Monday morning before standup
- End of sprint
- Anytime you need to report what was done for a project (Slack, email, weekly meeting)

## What I do

1. **Receive the project name** — must match a folder in `B01 Projects/` (case-insensitive)
2. **Determine the window:**
   - Default: last 7 days from today
   - `--days N` — last N days
   - `--since YYYY-MM-DD` — explicit start
3. **Scan three sources:**
   - Files in `B01 Projects/<project>/` modified in window → "Notas/runbooks novos"
   - Daily notes in window (`A04 Daily Notes/YYYY-MM-DD.md`) → extract task/log lines
   - Logs in `C04/03 Logs & Memory/` mentioning the project (commits, decisions, promotions)
4. **Build sections:**
   - ✅ **Concluído** — flat list of `[x]` items mentioning the project, deduplicated across days
   - 🔄 **Em andamento** — `[ ]` items still pending at end of window
   - 🚧 **Bloqueado / aguardando** — lines with keywords (`aguardando`, `waiting`, `blocked`, `bloqueado`)
   - 📝 **Notas/runbooks novos** — files in `B01 Projects/<project>/` created or significantly modified in window
   - 📅 **Por dia (task tree literal)** — verbatim task tree per daily note, filtered to project mentions
5. **Render output** in chosen format and either show in chat or save to file with `--save`

## Output: "Por dia" section — VERBATIM rule

For each daily note in the window:
- Extract the `## 📋 Tasks` section
- Filter line by line:
  - Line mentions the project (case-insensitive substring) → include
  - Line is an indented sub-task under an included parent → include
  - Otherwise → skip
- Preserve `[ ]` / `[x]` state, indentation, exact wording — **no summarizing, no rewording**
- If the daily has lines in `## 🗒️ Log` or `## 📖 Aprendizados` mentioning the project, surface them with `[log]` / `[aprendizado]` prefix
- Strip "Carried over from..." sentinel lines (they repeat across days, would inflate the recap)

## Rules

- **Verbatim on tasks.** Don't summarize ("4 tasks done" is forbidden). Show the literal tasks.
- **No invention.** Every line in the output traces back to a real line in a daily note or vault file.
- **No edits to source files.** Pure read + report.
- **Sub-task inclusion.** Sub-tasks under a parent that matched are included even if the sub-task itself doesn't mention the project. Inheritance.
- **Project name match is substring, case-insensitive.** "blindpay" matches "Blindpay", "BLINDPAY", "BlindPay - foo".

## Variations

- `/project-recap <name>` — last 7 days, markdown format
- `/project-recap <name> --days 14` — 2 weeks
- `/project-recap <name> --since 2026-04-20` — explicit start
- `/project-recap <name> --format slack` — bullet style, less markdown
- `/project-recap <name> --format md` — full markdown (default)
- `/project-recap <name> --format json` — structured for scripts
- `/project-recap <name> --save` — save report to `B05 Systems/recaps/<project>/YYYY-MM-DD.md` (auto-creates the project subfolder if missing)
- `/project-recap --all` — generate recap for every B01 project with activity in window (skip silent ones)

## Detection of "project activity"

A daily note "has activity" for a project if:
- A line in `## 📋 Tasks` contains the project name (substring match)
- A line in `## 🗒️ Log` or `## 📖 Aprendizados` contains the project name
- A wiki-link `[[<project>...]]` appears anywhere in the daily

A file in the vault counts as activity if:
- Path starts with `B01 Projects/<project>/`
- Modification time falls within the window

## Edge cases

- **Missing daily notes (weekend gaps):** show `(no daily)` for that date in the per-day section
- **Project name with spaces:** match folder name exactly (e.g., `umaletra` matches; `Uma Letra` does not unless the folder is named that)
- **Blocked detection:** heuristic via keywords. Surface in 🚧 section but never auto-classify a task as blocked without the keyword
- **`--all` with 10+ active projects:** warn first, ask for confirmation before generating multi-project report (it gets long)
- **Project name collision** (e.g., "stone" appears in many notes as a verb): if false positives are noisy, suggest creating an alias in the project folder's `_<project>.md` frontmatter

## Interaction example

```
you: /project-recap blindpay
me:  Window: 2026-04-21 → 2026-04-27 (7 days)
     Found activity in: 4 daily notes (Mon-gap, Tue-gap, Wed-Fri, Mon)
     Project files modified: 2 new runbooks
     
     [generates report — see below]

[Output]:
# Blindpay — recap (2026-04-21 → 2026-04-27)

## ✅ Concluído
- [x] Finish Veem on ledger
- [x] Add GET /i/veem/vba — PR #743 merged
- [x] Merge PR #738 (ledger poller)
- [x] Refactor by comment Bernardo
- [x] Fix development backoffice and ledger

## 🔄 Em andamento
- [ ] Merge PR #744 (ledger infra)
- [ ] Fix development for hit on sandbox in providers
- [ ] OpenFX polling

## 🚧 Bloqueado / aguardando
- Aguardando acesso ao OpenFX api doc (Bernardo) — desde 2026-04-24

## 📝 Notas/runbooks novos
- B01 Projects/blindpay/ledger/veem/how to deploy veem on ledger.md (2026-04-23)
- B01 Projects/blindpay/ledger/deploy_ledger vs sst deploy - task def drift.md (2026-04-23)

## 📅 Por dia

### Thu 2026-04-23
- [x] Blindpay - Finish Veem on ledger
- [x] Blindpay - Add GET /i/veem/vba to list active Veem VBAs - Merge the PR #743
- [x] Blindpay - Merge the PR #738 (ledger poller)
- [ ] Blindpay - Merge the PR #744 (ledger infra)
  - [x] Refactor by comment Bernardo
- [ ] Fix development for hit on sandbox in providers
- [x] Fix development backoffice and ledger

### Fri 2026-04-24
- [ ] Blindpay: Merge the PR #744 (ledger infra)
  - [x] Refactor by comment Bernardo
- [ ] Blindpay: Fix development for hit on sandbox in providers
- [log] I awaiting the access for OpenFX api doc (Bernardo)

### Sat-Sun 2026-04-25/26
- (no daily)

### Mon 2026-04-27
- [ ] Blindpay: Merge the PR #744 (ledger infra)
- [ ] Blindpay: Fix development for hit on sandbox in providers
```

## Automation

Schedule for Monday-morning standup:

```
/loop 7d /project-recap blindpay --save
```

Or run multiple at once:

```
/loop 7d /project-recap --all --save
```
