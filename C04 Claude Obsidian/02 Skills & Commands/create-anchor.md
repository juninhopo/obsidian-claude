---
name: create-anchor
description: Creates an Anchor Topic (MOC) from a given theme
invocation: "create anchor <topic>" | "/create-anchor <topic>"
---

# Skill: create-anchor

## When to use

You have a recurring theme in the vault and want to create its **entry point** (MOC) in A02.

Signs you need an anchor:
- 5+ notes in B03 on the same theme without linkage
- You "know you know" about X but can't find the right note
- The theme appears across multiple projects

## What I do

1. **Receive the theme** (e.g., "Observability")
2. **Search the vault** for all related notes (keywords, tags, content)
3. **Show candidates to you and surface obvious false positives** with a 1-line context snippet, so you reject them upfront
4. **Draft the structure** using `template-anchor-topic.md`:
   - Add the `<!-- anchor-meta -->` block with `search-terms` (what I used for grep) and `exclude-files` (whatever you rejected in step 3)
   - Fill the child-note list with approved links
   - Suggest sections (Fundamentals / Applied / Contrast)
   - Leave the **thesis blank** — authorship is yours
5. **Create the file** at `A02 Anchor Topics/<topic>.md`
6. **Report back:**
   - "Created A02/Observability.md with N suggested child notes"
   - "Write your thesis when you have a moment"
7. **Suggest backlinks:** "these N notes don't link back to the anchor. Want me to add `[[Observability]]` at the top?"
8. If you approve: add the backlinks.

The `anchor-meta` block lets `/refresh-anchor` run cleanly later — same seeds, same exclusions, no re-debate over false positives.

## Rules

- **I do not write your thesis.** I leave it blank.
- Only link existing notes (I don't invent connections).
- Order notes by modification date (most recent first).

## Variations

- `/create-anchor <topic>` — create from the vault
- `/create-anchor <topic> --from-backlog` — promote a note from A01/A05 into an anchor

## Interaction example

```
you: /create-anchor Observability
me:  Created A02 Anchor Topics/Observability.md
     Found 7 candidate child notes:
     - B03 Resources/studies/Development/Backend/logs.md
     - B01 Projects/blindpay/observability-plan.md
     - B04 Archives/abacatepay/observability quick starter.md
     ... 
     Thesis is blank — write it when you have a moment.
     Want me to add [[Observability]] to the 7 child notes?
```

## Suggested anchors for your vault

Based on what I see today, anchors that make sense:
- Observability
- Pix / Payments
- Zettelkasten / Second Brain
- Remote team management
- AWS / Infra
- Productivity
