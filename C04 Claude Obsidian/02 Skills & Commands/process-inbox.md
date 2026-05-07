---
name: process-inbox
description: Assisted triage of A00 Inbox — classifies each item and proposes a destination
invocation: "process my inbox" | "/process-inbox"
---

# Skill: process-inbox

## When to use

- Weekly (Friday or Sunday)
- When A00 Inbox has 10+ items
- Before a weekly review

## What I do when you invoke

1. **List** every file in `A00 Inbox/` (except README.md)
2. **Open each one** and read it
3. **Classify** each item into one of these categories:
   - 🗑️ Discard (not worth keeping)
   - 📥 Keep in A00 (not enough context yet)
   - 🏗️ A01 Processing (idea under construction)
   - ⚓ A02 Anchor Topic (becomes an MOC)
   - 📁 B01 Projects/<project> (goes to project X)
   - 📚 B03 Resources (reference note)
   - 💤 A05 Backlog ("some day" idea)
   - 🗄️ B04 Archives (historical/archive)
4. **Report** back in the format:

   ```
   === INBOX (N items) ===
   1. note-x.md → A01 Processing (reason: ...)
   2. photo-y.png → A03 Attachments (reason: ...)
   3. idea-z.md → DISCARD (reason: ...)
   ```

5. **Ask for confirmation** before moving. You can override the destination of any item.
6. **Execute** the `mv` operations after your approval.
7. **Update** `C04/03 Logs & Memory/inbox-log.md` with what was processed.

## Rules

- **I don't delete without confirmation.** Always ask.
- **I don't edit content.** I only move.
- **Verbatim on merge.** When the chosen destination is a merge (e.g., appending an inbox item's tasks into a project index), I preserve the original text exactly — wording, punctuation, whitespace, formatting. No tightening, no rewording, no translation. If the text needs fixing, I surface it as a diff suggestion AFTER the merge, and wait for you to edit. (Incident 2026-04-23: merging `Capibara Melhorias` into `_capibara.md`, I tightened 2 tasks on first pass and had to revert. Don't repeat.)
- If I don't know where it goes, I leave it in A00 with a `#to-decide` tag.

## Variations

- `/process-inbox quick` — quick mode: move only obvious ones, leave ambiguous in A00
- `/process-inbox full` — full mode: propose destination for each item and wait for approval

## Suggested next steps after running

1. Run `/weekly-review` if it's end of week
2. If many items went to A01, run `/promote-to-anchor` on some
3. Review C04/01 Outputs if I generated any analysis
