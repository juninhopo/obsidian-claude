---
name: calendar-week
description: Shows the upcoming week of meetings, grouped by day, with free blocks during work hours
invocation: "calendar week" | "this week's calendar" | "/calendar-week"
---

# Skill: calendar-week

## When to use

- **Monday morning** planning — what does the week look like?
- **Before scheduling** a new meeting — when is the user free?
- When user asks: *"estou livre amanhã às 14h?"*, *"que dia tenho menos reunião?"*, *"como tá minha semana?"*

## Prerequisite

Google Calendar MCP server (`claude.ai Google Calendar`) connected. Run `/mcp` to verify. If not connected, abort and tell the user to authenticate.

## What I do when invoked

1. **Resolve the date range:**
   - Default: 7 days from today (today + 6)
   - `--days N` overrides span (e.g. `--days 14` for two weeks)
   - `--from YYYY-MM-DD` overrides start date
2. **Pull events** for the range via `mcp__claude_ai_Google_Calendar__list_events`:
   - `startTime`: `<from>T00:00:00-03:00`
   - `endTime`: `<to>T23:59:59-03:00`
   - `timeZone`: `America/Sao_Paulo`
   - `orderBy`: `startTime`
   - `pageSize`: 250
3. **Filter out** events where user's `responseStatus == "declined"` and all-day `transparent` events (always-on rooms — too noisy for week view).
4. **Group** events by date (local TZ).
5. **For each day**, compute:
   - Total scheduled meeting time (sum of durations)
   - Free blocks during work hours (default 09:00–18:00 BRT, configurable)
6. **Render** the report (see Output format).

## Output format

```markdown
## Week of YYYY-MM-DD (Monday) — total: Xh of meetings

### Mon DD/MM (today)
- 09:00–10:00 — Standup ([meet](url))
- 13:30–14:00 — Daily ([meet](url))
**Total:** 1h30 · **Free blocks:** 10:00–13:30, 14:00–18:00 (7h30)

### Tue DD/MM
- 11:00–12:00 — 1:1 with Bernardo ([meet](url))
**Total:** 1h · **Free blocks:** 09:00–11:00, 12:00–18:00 (8h)

### Wed DD/MM
*No meetings scheduled* ✨
**Free:** 09:00–18:00 (9h)

...
```

## Variations

- `/calendar-week` — next 7 days (today + 6)
- `/calendar-week --days 14` — next 14 days
- `/calendar-week --from 2026-05-04` — week starting on a specific Monday
- `/calendar-week --busy` — only show days with ≥3 meetings (signal-only)
- `/calendar-week --free-only` — only list free blocks, no meeting list
- `/calendar-week --work-hours 10-19` — adjust work-hour window for free-block calc

## Heuristics

- **Work hours default:** 09:00–18:00 in `America/Sao_Paulo`. Override via `--work-hours HH-HH`.
- **Free block:** any window ≥30 min between events within work hours.
- **All-day declined / transparent events:** filtered.
- **Recurring events:** show each instance; the MCP returns expanded recurrences.

## Rules

- **Don't write to disk** unless the user asks. This is a read-only report by default.
- **No invention.** If the MCP returns no events, report "No meetings this week" — don't fabricate context.
- **Privacy.** If the user shares the report (Slack, etc.), strip sensitive event titles unless they're already public.

## Interaction example

```
you: /calendar-week
me:  Week of 2026-04-27 (Monday) — total: 4h15 of meetings

     ### Mon 27/04
     - 13:30–14:30 — Weekly ([meet](url))
     **Total:** 1h · Free: 09:00–13:30, 14:30–18:00 (8h)

     ### Tue 28/04 (today)
     - 13:30–14:00 — Daily ([meet](url))
     **Total:** 30min · Free: 09:00–13:30, 14:00–18:00 (8h30)

     ### Wed 29/04 — Thu 30/04 — Fri 01/05
     *No meetings yet*
     **Free:** 9h each day

     Lightest day: Wednesday. Heaviest: Monday.
```

## Suggested follow-ups after running

1. If days look heavy → consider declining or moving non-essential meetings
2. Block free blocks for deep work (`/calendar-week` shows where they are)
3. Pair with `/process-meetings` after Monday's weekly to import to `B06 Meetings/`
