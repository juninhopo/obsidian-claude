---
name: link-suggestions
description: Propose wiki-links for a given note based on vault content
invocation: "suggest links for <note>" | "/link-suggestions <note>"
---

# Skill: link-suggestions

## When to use

- You wrote a note and want to connect it to the rest of the vault
- You promoted a note to B03/A02 but it has no links yet
- You want to find "what else have I written about this"

## What I do

1. **Read the target note** completely
2. **Extract candidate keywords:**
   - Title tokens
   - Frequent nouns/proper nouns in the body
   - Existing wiki-links (to exclude from suggestions)
   - Technical terms (project names, tools, concepts)
3. **Search the vault** for other notes mentioning these terms
4. **Rank candidates** by:
   - Keyword overlap (more matches = higher rank)
   - Note location (A02 anchors weigh more — they are MOCs)
   - Recency of the target note's topic in that candidate
5. **Report** a ranked list with the context line that triggered each match:

   ```
   === LINK SUGGESTIONS for "logs-vs-metrics.md" ===
   
   High confidence:
   1. [[Observability]] — A02 anchor (same domain)
      Context: "three pillars of observability: logs, metrics, traces"
   2. [[observability plan]] — B01/abacatepay
      Context: "loki for logs, prometheus for metrics..."
   
   Medium confidence:
   3. [[datadog-notes]] — B03 resource
      Context: "metrics dashboard configuration"
   ```
6. **Do NOT auto-insert.** Wait for your selection.
7. After your approval: **insert the links** where semantically relevant, or append a "Related" section at the bottom.

## Rules

- **No inventing links.** Only link to notes that already exist.
- **No rewriting.** I only add a link — existing text stays intact.
- **Explicit placement.** Ask where to insert (inline vs. Related section) unless obvious.

## Variations

- `/link-suggestions <note>` — standard
- `/link-suggestions <note> --limit 5` — cap to top 5
- `/link-suggestions <note> --anchors-only` — only suggest A02 anchors (not raw notes)
- `/link-suggestions <note> --from <folder>` — restrict search to a specific folder

## Interaction example

```
you: /link-suggestions B03 Resources/studies/Development/Backend/logs.md
me:  Analyzed the note (1.2k words, 0 existing links).
     Found 8 candidate links — top 3 shown.
     
     1. [[Observability]] (A02 anchor) — HIGH
     2. [[observability plan]] (B01) — HIGH
     3. [[loki-setup]] (B03) — MEDIUM
     ...
     
     Full list in C04/03 Logs & Memory/link-suggestions-logs-2026-04-23.md
     
     Which to insert? (all / 1,2 / none)
     Placement? (inline / Related section)
```
