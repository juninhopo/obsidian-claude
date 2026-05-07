---
name: process-meetings
description: Import new meetings from Granola into B06 Meetings/ — convert ProseMirror→markdown, populate frontmatter, dedupe by granola-id
invocation: "process meetings" | "/process-meetings"
---

# Skill: process-meetings

## When to use

- When meetings exist in Granola that haven't been imported into `B06 Meetings/` yet
- Recurring: weekly after weekly review, or ad-hoc after an important meeting
- Initial bulk: run once to import all old meetings
- Automation: `/loop 1d /process-meetings` for daily sync

## Source of truth

`~/Library/Application Support/Granola/cache-v6.json`

Structure used:
- `cache.state.documents` — dict `<doc_id>` → metadata + notes (ProseMirror JSON)
- `cache.state.transcripts` — dict `<doc_id>` → list of segments (only meetings that were transcribed)
- `cache.state.meetingsMetadata` — extra metadata per meeting

## What I do when invoked

1. **Read** `cache-v6.json` and extract `cache.state.documents`.
2. **Filter** documents:
   - Skip if `deleted_at != null`
   - Skip if `valid_meeting == false`
   - Skip if `type != "meeting"` (non-meeting notes)
3. **For each valid doc**, look up `B06 Meetings/` for an existing file with frontmatter `granola-id: <doc.id>`:
   ```bash
   grep -r "granola-id: <id>" "B06 Meetings/" --include="*.md"
   ```
   - If exists → skip (unless `--force` or `--update`)
   - If not → create (step 4)
4. **Create the file**:
   - Path: `B06 Meetings/YYYY/MM/YYYY-MM-DD-<slug>.md` (create YYYY/MM if missing)
   - Slug: kebab-case of `title`. "Daily"/"Weekly"/"untitled" preserved. Same-day collision → suffix `-2`, `-3`.
   - Content: apply `C04/06 Templates & References/template-meeting.md` + populate (step 5).
5. **Populate the template**:
   - **Frontmatter**:
     - `date`: `created_at[:10]`
     - `type`: heuristic — title contains "Weekly" → `weekly`, "Daily" → `daily`, pattern "X & Y" or "X e Y" → `1on1`, otherwise → `other`
     - `project`: `null` (user fills in later)
     - `participants`: extract from `doc.people.attendees[].name` or `.email` (excluding the user owner)
     - `source`: `granola`
     - `granola-id`: `doc.id`
     - `status`: `raw` if there's a source (transcript or notes), else `empty` (placeholder — Granola registered the event but no content)
     - `ai-generated`, `ai-summary-date`, `ai-summary-source`, `ai-summary-language`: only set when summary runs (step 6)
   - **Body** (in this order):
     - `## Summary`: filled in step 6 (if there's transcript or notes), else empty.
     - `## Notes`: converted from `doc.notes` (ProseMirror JSON) to markdown — see conversion rules below.
     - `## Links`: populated from `google_calendar_event` (calendar URL, hangout/Meet URL).
     - `## Transcript`: if `transcripts[doc.id]` exists, concatenate `text` ordered by `start_timestamp`. If >50KB, save as separate file `B06 Meetings/YYYY/MM/transcripts/YYYY-MM-DD-<slug>.transcript.md` and link.
6. **Generate AI summary** (only if transcript or notes exist — skip otherwise):
   - Detect the meeting's primary language from the transcript/notes (pt-BR, en, es, etc.).
   - Produce a structured summary in the **meeting's language** (don't translate). Format mirrors what Granola produces:
     - Group content by **theme** (e.g. "Migração de Infraestrutura", "Questões Legais", "Desenvolvimento", "Financeiro", "Vendas") — not by speaker.
     - Each theme gets `###` subheading + bullets with sub-bullets for nuance.
     - Mention people in **bold** when introducing what they did/decided/are working on (e.g. `**Darlan**: terminou OpenFX, próximo é One Money`).
     - Final subsection `### Próximos Passos` (or `### Next Steps` if meeting is in English) — tasks grouped by person, only items the meeting **explicitly assigned**. No invention.
   - Place the AI callout marker **above** the structured content (not wrapping it — keeps `###` headings clean):
     ```markdown
     ## Summary

     > [!ai]+ AI draft — generated YYYY-MM-DD from transcript. Review and accept/edit.

     ### {Theme 1 in meeting language}

     - Bullet point with key info
       - Sub-bullet for detail
     - **{Person}**: what they did / what they're working on

     ### {Theme 2}

     - ...

     ### Próximos Passos

     - **{Person}**: explicit action item
     - **{Person}**: explicit action item
     ```
     When the user accepts the summary, they delete only the callout line.
   - Add to frontmatter:
     ```yaml
     ai-generated: summary
     ai-summary-date: YYYY-MM-DD
     ai-summary-source: transcript | notes
     ai-summary-language: pt-BR | en | es | ...
     ```
   - **Never** auto-mark `status: processed` — only the user does that, after reviewing the AI draft.
   - **Never** invent. Only include facts the transcript/notes actually contain. If a name or fact is unclear, prefer omission to guessing.
7. **Update** `B06 Meetings/_meetings.md`:
   - Add entries with source under `### Raw (not processed)` with `[[YYYY-MM-DD-slug]]` (use 🎙️ marker if it has transcript).
   - Add entries without source (`status: empty`) under `### Empty (no source)` inside the collapsed `> [!info]-` callout — keeps the index tidy.
8. **Log** in `C04 Claude Obsidian/03 Logs & Memory/process-meetings-YYYY-MM-DD.md`:
   - List of imported, skipped, errored docs. Note which ones got AI summary vs which were import-only.
9. **Report** at the end:
   ```
   === Process meetings ===
   ✅ Imported: N
   ⏭️  Already existed: M
   🚫 Invalid/skipped: K
   📁 Total in Granola: N+M+K

   New in B06:
   - 2026-04-27 weekly                  [granola]
   - 2026-04-27 aulao-de-obsidian       [granola]
   ...
   ```

## ProseMirror → Markdown (conversion rules)

`doc.notes` has format `{"type": "doc", "content": [...]}`. Conversion:

| Node type | Markdown output |
|-----------|-----------------|
| `doc` | concatenate `content` separated by `\n\n` |
| `paragraph` | inline text + `\n` |
| `heading` (attrs.level) | `#` × level + ` ` + text |
| `bullet_list` | each `list_item` as `- ` + content |
| `ordered_list` | each `list_item` as `1. ` + content |
| `list_item` | inline content |
| `code_block` | ` ```\n` + `text` + `\n``` ` |
| `blockquote` | `> ` + text |
| `hard_break` | `  \n` |
| `text` | `text` (with marks applied) |

**Marks** in `text.marks[]`:
- `bold` → `**text**`
- `italic` → `*text*`
- `code` → `` `text` ``
- `link` (attrs.href) → `[text](href)`
- `strike` → `~~text~~`

Practical implementation: use ad-hoc Python — recursive `prosemirror_to_md(node)` function during execution. Don't create a reusable script file (overkill for a skill).

## Heuristics and fallbacks

- **Empty slug** (title is null/empty/"untitled"): use `untitled-<timestamp_short>` where timestamp is `HHMM` from `created_at`.
- **Title with special characters**: strip accents, keep alphanumeric + hyphen.
- **Empty participants**: omit field from frontmatter (don't write `participants: []`).
- **Missing `google_calendar_event`**: `participants` is empty.
- **Very large transcript** (>50KB): separate file, link from the main note.

## Variations

- `/process-meetings` — only import new ones (default)
- `/process-meetings --force` — overwrite existing files (careful: loses user edits)
- `/process-meetings --update` — only refresh frontmatter fields of existing files, preserve body
- `/process-meetings --since YYYY-MM-DD` — date filter
- `/process-meetings --dry-run` — show plan, write nothing
- `/process-meetings --doc <granola-id>` — import a single specific meeting

## Rules

- **Verbatim** for notes and transcript: content from Granola is format-converted, not rewritten. No tightening, no translation. The transcript stays raw.
- **AI summary is clearly marked as AI**. Always wrap with `> [!ai]+ AI draft` callout and add `ai-generated` keys to frontmatter. The user must be able to tell at a glance which content is mine and which is theirs.
- **AI summary is a draft, not a decision**. I propose; user reviews → accepts (removes callout) or edits or deletes. `status` only flips to `processed` when the user reviews.
- **No AI summary if there's no source** (no transcript and no notes). Leave sections empty with the standard placeholder comment.
- **Dedupe by `granola-id`** (primary key). Duplicate titles are fine; ID is unique.
- **Read-only on Granola**. Never modify `cache-v6.json`.
- **No deletes** in `B06`. If a doc was marked `deleted_at` in Granola after import, I flag it in the log but don't remove the file.
- **Confirm before large bulk import** (>20 new docs) — show the list, wait for approval.

## AI summary prompt

When generating summary, use this single unified prompt (input = transcript or notes):

> Read the meeting transcript/notes below. Produce a structured summary that mirrors the format Granola uses, in the **same language as the meeting** (do not translate).
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
> ### Próximos Passos   ← (use "Next Steps" if the meeting is in English)
>
> - **{Person}**: action item explicitly stated in the meeting
> - **{Person}**: another action item
> ```
>
> Rules:
> - Group bullets by theme (e.g. "Migração", "Questões Legais", "Desenvolvimento", "Design", "Financeiro", "Vendas"), not by speaker.
> - Use **bold** for person names when introducing their work or status.
> - Use sub-bullets for nuance (numbers, sub-decisions, timing).
> - In `Próximos Passos`, include only action items the meeting explicitly assigned — never infer or invent.
> - If a name in the transcript is unclear or partially heard, prefer omission to guessing.
> - Do not include "Decisions" or "Topics" as separate sections — fold them into the thematic narrative.
> - Output only the structured summary. No preamble, no closing remarks.

Faithfulness over completeness: a shorter accurate summary beats a longer one with invented details.

## Edge cases

- **Granola open / cache locked**: the JSON is stable, but if Granola is writing the read may catch an inconsistent state. Recommend closing the app before a large bulk import.
- **Meetings with same title on the same day**: numeric suffix in slug (`-2`, `-3`). `granola-id` in frontmatter disambiguates.
- **Meetings without `notes` (empty)**: import anyway, body has empty sections. It's a marker that the meeting happened.
- **Repeated `untitled`**: use `created_at` timestamp (HHMM) in slug — `untitled-1430`.

## Interaction example

```
you: /process-meetings
me:  Reading Granola cache (44 documents)...

     Plan:
     ✅ To import: 44 new meetings
     ⏭️  Already in B06: 0
     🚫 Skipped (deleted/invalid): 0

     Distribution:
     - Daily: 18
     - Weekly: 6
     - 1on1: 1 (Darlan & Paulo)
     - Other: 19 (including "Disaster Recovery", "Zenus and Portage tests", etc.)

     Large bulk (>20). Confirm? (y / dry-run / abort)

you: y
me:  [import...] 44/44 imported. Log at C04/03/process-meetings-2026-04-27.md.
     _meetings.md updated with Raw entries.
```

## Suggested next steps after running

1. Open `B06 Meetings/_meetings.md` and review the raw list
2. For each raw that matters: open → review the AI summary draft (themed sections + Próximos Passos) → accept/edit → remove the `> [!ai]+` callout line → change status to `processed`
3. Relevant action items: move (verbatim) into `B01 Projects/<project>/_<project>.md`
4. New terms / people: propose addition to `B01 Projects/<project>/CLAUDE.md` (Pattern capture rule)
