---
name: create-daily
description: Creates today's daily note from the template, pulls yesterday's unfinished tasks, and populates today's meetings from Google Calendar
invocation: "today's daily" | "/create-daily"
---

# Skill: create-daily

## When to use

Start of the day, before beginning work.

## What I do when invoked

1. **Resolve the target date** (today by default; see Variations).
2. **Check if `A04 Daily Notes/YYYY-MM-DD.md` already exists.**
   - If it does → open for the user to continue. Skip creation, but still run steps 5–6 if sections are empty.
3. **If it doesn't exist**, create it from `C04 Claude Obsidian/06 Templates & References/template-daily-note.md`:
   - Replace `{{date:YYYY-MM-DD}}` with the target date
   - Replace `{{date:dddd}}` with the English day of week (Monday, Tuesday, …)
   - Replace `{{date-1d:YYYY-MM-DD}}` with the previous date
   - Replace `{{date+1d:YYYY-MM-DD}}` with the next date
4. **Carry over from previous day** (`A04 Daily Notes/YYYY-MM-DD.md` of `target_date - 1`, or skip back further if weekend gap):
   - Read yesterday's `## 📋 Tasks` section.
   - Take all `- [ ]` items (unfinished). Keep nested `- [x]` sub-tasks intact (they show progress).
   - Drop `- [x]` parent tasks (already done).
   - Insert under `*Carried over from YYYY-MM-DD (unfinished):*` — preserve original wording verbatim (no rewriting).
5. **Pull today's meetings from Google Calendar** (see "Calendar integration" below). Populate the `## 📅 Meetings` section.
6. **Report**: print the file path + summary (`N tasks carried over`, `M meetings scheduled`).

## Calendar integration

The Google Calendar MCP server (`claude.ai Google Calendar`) must be connected — run `/mcp` to verify. If not connected, skip step 5 silently and note "calendar not connected" in the report.

When connected, after creating the daily file:

1. Call `mcp__claude_ai_Google_Calendar__list_events` with:
   - `startTime`: `<target_date>T00:00:00-03:00` (America/Sao_Paulo offset; adjust if user's TZ differs)
   - `endTime`: `<target_date>T23:59:59-03:00`
   - `timeZone`: `America/Sao_Paulo`
   - `orderBy`: `startTime`
2. For each event, format a bullet:
   - **Timed event** (has `start.dateTime`):
     `- **HH:MM–HH:MM** — [Title](conferenceUrl) (organizer first name)`
   - **All-day event** (`start.date` only) with `transparency: "transparent"`:
     `- **All day** — [Title](conferenceUrl) (always-on, organizer first name)`
   - **All-day event** (blocking, not transparent):
     `- **All day** — [Title](conferenceUrl)`
3. **Filter out** events where the user's own `responseStatus` is `"declined"` (don't pollute the daily with meetings the user explicitly skipped).
4. **Replace** the placeholder `- ` under `## 📅 Meetings` with the formatted list. If no events, leave the empty bullet alone.
5. The `conferenceUrl` may be missing for some events — in that case, link to `htmlLink` (Google Calendar event URL) instead, with title `[Title](calendar-url)`.

## Standard structure

See `C04 Claude Obsidian/06 Templates & References/template-daily-note.md`.

## Variations

- `/create-daily` — creates today
- `/create-daily tomorrow` — creates tomorrow (planning ahead)
- `/create-daily 2026-04-25` — specific date
- `/create-daily --no-calendar` — skip Calendar pull (faster; useful when offline)
- `/create-daily --refresh-meetings` — re-pull Calendar even if file exists

## Rules

- **Verbatim** when carrying over tasks from yesterday — preserve wording, formatting, indentation, sub-task state. Do not tighten or reword.
- **Don't invent meetings** — if the Calendar MCP returns nothing, the section stays empty.
- **Don't auto-mark anything as done** — only the user marks `[x]`.

## Integration with Obsidian core Daily Notes plugin

If the core plugin **Daily Notes** is active, it creates the file from the template. This skill adds value by:
- Carrying over unfinished tasks
- Populating Calendar events
- Pulling tasks from active project indexes (future enhancement)

## Automation

To create automatically every morning:

- **Obsidian core plugin** → Daily Notes → "Open daily note on startup"
- **Claude Code loop**: `/loop 1d /create-daily` (every 24h)
- **Cron**: schedule `/create-daily` at 07:00 on weekdays

## Interaction example

```
you: /create-daily
me:  Created A04 Daily Notes/2026-04-28.md (Tuesday).
     ✓ Carried over 4 unfinished tasks from 2026-04-27
     ✓ Pulled 2 meetings from Google Calendar
     - All day — BlindPay Office Room
     - 13:30–14:00 — Daily
```

## Suggested next steps after running

1. Open the daily, set the **Focus of the day** (single sentence)
2. Triage carried-over tasks — drop irrelevant, prioritize the rest
3. At end of day: run the "Quick end-of-day review" checklist
