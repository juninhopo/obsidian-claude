---
name: process-meetings
description: Import new meetings from Granola into B06 Meetings/ via the official Granola MCP — populate frontmatter, dedupe by granola-id
invocation: "process meetings" | "/process-meetings"
---

# Skill: process-meetings

## When to use

- When meetings exist in Granola that haven't been imported into `B06 Meetings/` yet
- Recurring: weekly after weekly review, or ad-hoc after an important meeting
- Initial bulk: run once to import all old meetings
- Automation: `/loop 1d /process-meetings` for daily sync

## Source of truth

**Granola MCP** — `https://mcp.granola.ai/mcp` (HTTP streamable + OAuth 2.0 DCR; configured at user scope in Claude Code).

See **HOW TO USE.md → MCPs (external integrations)** for setup. Quick check before any run:

```
mcp__granola__get_account_info
```

Should return your Granola email + active workspace. If not connected, instruct the user to run `claude mcp add -s user --transport http granola https://mcp.granola.ai/mcp` and complete OAuth via `/mcp`.

### MCP tools used by this skill

| Tool | Purpose |
|------|---------|
| `mcp__granola__get_account_info` | Verify auth + active workspace |
| `mcp__granola__list_meetings` | List meetings in a date range. Use `time_range: "last_30_days"` for default sync, or `custom` + `custom_start`/`custom_end` for backfill |
| `mcp__granola__get_meetings` | Batch fetch metadata + AI summary for up to 10 meeting IDs |
| `mcp__granola__get_meeting_transcript` | Fetch full verbatim transcript for one meeting |
| `mcp__granola__list_meeting_folders` | (Optional) list Granola folders if filtering by folder |
| `mcp__granola__query_granola_meetings` | (Optional) RAG search across recent meetings — useful for "did anyone mention X?" questions, not for bulk import |

> ⚠️ **Why not the local cache?** Until 2026-05-06, this skill read `~/Library/Application Support/Granola/cache-v6.json`. Granola v7 encrypted the local cache (`cache-v6.json.enc` via Electron safeStorage + macOS Keychain), and the plain JSON stopped being updated. Reading the encrypted file is undocumented and the dedicated community plugins were marked DEPRECATED. The Granola team officially recommends the Public API / MCP, which is what this skill now uses.

## What I do when invoked

1. **Verify auth.** Call `get_account_info`. Abort with a clear message if not connected.

2. **List meetings.** Call `list_meetings` with the appropriate range:
   - Default (no args): `time_range: "last_30_days"`
   - `--since YYYY-MM-DD`: `time_range: "custom"`, `custom_start: <date>`, `custom_end: today`
   - `--doc <granola-id>`: skip list, jump to step 4 with that single ID

3. **Dedupe.** For each meeting in the list, look up `B06 Meetings/` for an existing file with frontmatter `granola-id: <doc.id>`:
   ```bash
   grep -r "granola-id: <id>" "B06 Meetings/" --include="*.md"
   ```
   - If exists → skip (unless `--force` or `--update`)
   - If not → create (step 4)

4. **Fetch details.** Call `get_meetings` with the IDs to import (max 10 per call — batch if more). For each meeting, also call `get_meeting_transcript` to get the verbatim transcript.

5. **Create the file.**
   - Path: `B06 Meetings/YYYY/MM/YYYY-MM-DD-<slug>.md` (create YYYY/MM if missing)
   - Slug: kebab-case of `title`. "Daily"/"Weekly"/"untitled" preserved. Same-day collision → suffix `-2`, `-3`.
   - Content: apply `C04/06 Templates & References/template-meeting.md` + populate (step 6).

6. **Populate the template.**
   - **Frontmatter**:
     - `date`: meeting date (`YYYY-MM-DD`) from the list response
     - `type`: heuristic — title contains "Weekly" → `weekly`, "Daily" → `daily`, single named participant or pattern "X & Y" / "X e Y" → `1on1`, otherwise → `other`
     - `project`: `null` (user fills in later — except: if all participants share a single email domain that matches an active project alias, default to that project)
     - `participants`: extract emails from `<known_participants>` (excluding the note creator's email when feasible)
     - `source`: `granola`
     - `granola-id`: the meeting ID from MCP
     - `status`: `raw` if there's a transcript or summary, else `empty`
     - `ai-generated`, `ai-summary-date`, `ai-summary-source`, `ai-summary-language`: only set when the user-facing summary runs (step 7)
   - **Body** (in this order):
     - `## Summary`: filled in step 7 — English structured summary (see prompt below). If the meeting has no transcript and no Granola summary, leave empty with a placeholder comment.
     - `## Notes`: the verbatim Granola `<summary>` content from `get_meetings` response, in its original language. Header is `## Notes` for legacy compatibility — this is Granola's native AI summary, kept as-is for traceability.
     - `## Links`: populated from any URLs in the meeting metadata (calendar, hangout/Meet).
     - `## Transcript`: if a transcript exists → save as separate file `B06 Meetings/YYYY/MM/transcripts/YYYY-MM-DD-<slug>.transcript.md` and link from the main note as `→ [[transcripts/YYYY-MM-DD-<slug>.transcript]]`. **All transcripts now go to the separate file by default** (matches the post-2026-05-05 vault pattern). Inline only if user explicitly passes `--inline-transcript`.

7. **Generate AI summary** (only if transcript exists — skip if just notes/no source):
   - **Always write the summary in English**, regardless of the meeting's spoken language. Do not translate verbatim quotes, but section headings, bullets, and narrative must be in English.
   - Format mirrors what Granola produces but rewritten in English:
     - Group content by **theme** (e.g. "Infrastructure Migration", "Legal", "Development", "Finance", "Sales") — not by speaker.
     - Each theme gets `###` subheading + bullets with sub-bullets for nuance.
     - Mention people in **bold** when introducing what they did/decided/are working on (e.g. `**Alice**: finished provider integration, next is settlement`).
     - Final subsection `### Next Steps` — tasks grouped by person, only items the meeting **explicitly assigned**. No invention.
   - Place the AI callout marker **above** the structured content (not wrapping it — keeps `###` headings clean):
     ```markdown
     ## Summary

     > [!ai]+ AI draft — generated YYYY-MM-DD from transcript. Review and accept/edit.

     ### {Theme 1}

     - Bullet point with key info
       - Sub-bullet for detail
     - **{Person}**: what they did / what they're working on

     ### {Theme 2}

     - ...

     ### Next Steps

     - **{Person}**: explicit action item
     - **{Person}**: explicit action item
     ```
     When the user accepts the summary, they delete only the callout line.
   - Add to frontmatter:
     ```yaml
     ai-generated:
       - summary
     ai-summary-date: YYYY-MM-DD
     ai-summary-source: transcript
     ai-summary-language: pt-BR | en | es | ...
     ```
   - **Never** auto-mark `status: processed` — only the user does that, after reviewing the AI draft.
   - **Never** invent. Only include facts the transcript actually contains. If a name or fact is unclear, prefer omission to guessing.

8. **Update `B06 Meetings/_meetings.md`:**
   - Add entries under `### Raw (not processed)` with `[[YYYY-MM-DD-slug]]` (use 🎙️ marker if it has a transcript file).
   - Add entries without source (`status: empty`) under `### Empty (no source)` inside the collapsed `> [!info]-` callout — keeps the index tidy.
   - Update `last-updated` in frontmatter.

9. **Log** in `C04 Claude Obsidian/03 Logs & Memory/process-meetings-YYYY-MM-DD.md`:
   - List of imported, skipped, errored docs. Note which got AI summary vs which were import-only.
   - Append to "Import history" in `_meetings.md`.

10. **Report** at the end:
    ```
    === Process meetings ===
    ✅ Imported: N
    ⏭️  Already existed: M
    🚫 Invalid/skipped: K
    📁 Total in Granola (range): N+M+K

    New in B06:
    - 2026-05-07 daily                                [granola] 🎙️
    - 2026-05-07 product-review                       [granola] 🎙️
    ...
    ```

## Heuristics and fallbacks

- **Empty slug** (title is null/empty/"untitled"): use `untitled-<HHMM>` from the meeting's start time.
- **Title with special characters**: strip accents, keep alphanumeric + hyphen.
- **Empty participants**: omit field from frontmatter (don't write `participants: []`).
- **Missing calendar URL**: leave `## Links` with a single `-` placeholder.
- **All transcripts go to separate file** (post-2026-05-05 convention). Default folder: `B06 Meetings/YYYY/MM/transcripts/`.

## Variations

- `/process-meetings` — only import new from last 30 days (default)
- `/process-meetings --force` — overwrite existing files (careful: loses user edits)
- `/process-meetings --update` — only refresh frontmatter fields of existing files, preserve body
- `/process-meetings --since YYYY-MM-DD` — date filter (uses `custom` time range)
- `/process-meetings --dry-run` — show plan, write nothing
- `/process-meetings --doc <granola-id>` — import a single specific meeting
- `/process-meetings --inline-transcript` — keep transcript inline in main note (legacy behavior, use only if you really want it)

## Rules

- **Verbatim** for transcript and Granola's native summary (`## Notes`): content from Granola is copied as-is, not rewritten. No tightening, no translation. The transcript stays raw.
- **AI summary is clearly marked as AI**. Always wrap with `> [!ai]+ AI draft` callout and add `ai-generated` keys to frontmatter. The user must be able to tell at a glance which content is mine and which is theirs.
- **AI summary is a draft, not a decision**. I propose; user reviews → accepts (removes callout) or edits or deletes. `status` only flips to `processed` when the user reviews.
- **No AI summary if there's no transcript** (Granola's native summary alone goes into `## Notes`, but I don't generate an English structured summary from notes-only — too lossy).
- **Dedupe by `granola-id`** (primary key). Duplicate titles are fine; ID is unique.
- **Read-only on Granola**. Never call mutating MCP tools (none exposed today, but be mindful).
- **No deletes** in `B06`. If a doc is gone from Granola after import (deleted on their side), I flag it in the log but don't remove the file.
- **Confirm before large bulk import** (>20 new docs) — show the list, wait for approval.

## AI summary prompt

When generating the English summary, use this single unified prompt (input = transcript):

> Read the meeting transcript below. Produce a structured summary **in English**, regardless of the language spoken in the meeting.
>
> Format:
> ```
> ### {Theme 1}
>
> - Key point about this theme
>   - Sub-bullet for detail
> - **{Person Name}**: what they did or are working on (only when relevant)
>
> ### {Theme 2}
>
> - ...
>
> ### Next Steps
>
> - **{Person}**: action item explicitly stated in the meeting
> - **{Person}**: another action item
> ```
>
> Rules:
> - **Always write in English** — even if the meeting was in pt-BR, Spanish, or any other language.
> - Group bullets by theme (e.g. "Development", "Legal & Compliance", "Commercial", "Finance", "Infrastructure"), not by speaker.
> - Use **bold** for person names when introducing their work or status.
> - Use sub-bullets for nuance (numbers, sub-decisions, timing).
> - In `Next Steps`, include only action items the meeting explicitly assigned — never infer or invent.
> - If a name in the transcript is unclear or partially heard, prefer omission to guessing.
> - Do not include "Decisions" or "Topics" as separate sections — fold them into the thematic narrative.
> - Output only the structured summary. No preamble, no closing remarks.

Faithfulness over completeness: a shorter accurate summary beats a longer one with invented details.

## Edge cases

- **MCP not connected / token expired:** abort with a clear message. Do not fall back to the local cache (it's encrypted and stale anyway).
- **Rate limits:** the Granola Public API has per-workspace rate limits. If `list_meetings` or `get_meetings` returns rate-limit errors, batch smaller and back off.
- **Meetings with same title on the same day:** numeric suffix in slug (`-2`, `-3`). `granola-id` in frontmatter disambiguates.
- **Meetings without transcript:** import anyway, body has `## Summary` empty, `## Notes` populated from Granola summary, no `## Transcript` section (or empty placeholder). It's a marker that the meeting happened.
- **Repeated `untitled`:** use start time (HHMM) in slug — `untitled-1430`.

## Interaction example

```
you: /process-meetings
me:  Verifying Granola MCP connection...
     ✅ Connected as <your-granola-email> (workspace: <your-workspace>)

     Listing meetings (last_30_days)...
     Found N meetings in range.

     Plan:
     ✅ To import: 3 new
     ⏭️  Already in B06: N-3
     🚫 Skipped (deleted/invalid): 0

     New:
     - 2026-05-09 weekly                                [granola]
     - 2026-05-09 product-review                        [granola] 🎙️
     - 2026-05-09 untitled-1543                         [granola] 🎙️

     Confirm? (y / dry-run / abort)

you: y
me:  [import...] 3/3 imported. Log at C04/03/process-meetings-YYYY-MM-DD.md.
     _meetings.md updated with Raw entries.
```

## Suggested next steps after running

1. Open `B06 Meetings/_meetings.md` and review the Raw list
2. For each raw that matters: open → review the AI Summary draft (themed sections + Next Steps) → accept/edit → remove the `> [!ai]+` callout line → change `status` to `processed`
3. Relevant action items: move (verbatim) into `B01 Projects/<project>/_<project>.md`
4. New terms / people: propose addition to `B01 Projects/<project>/CLAUDE.md` (Pattern capture rule)

## History

- 2026-04-27: skill created reading from `~/Library/Application Support/Granola/cache-v6.json`
- 2026-05-09: **migrated source of truth from local cache → Granola MCP** after Granola v7 encrypted the local cache. ProseMirror conversion logic removed (MCP returns ready-to-use markdown summary + plain transcript text).
