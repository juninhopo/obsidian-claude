---
name: refresh-anchor
description: Audit an Anchor Topic against the current vault and propose updates (new links, broken links) without touching your thesis
invocation: "refresh anchor <topic>" | "/refresh-anchor <topic>"
---

# Skill: refresh-anchor

## When to use

- Quarterly anchor review (is this MOC still accurate?)
- After a big reorganization (renames, moves, archives)
- When you feel "I know I wrote about X, but the anchor doesn't show it"
- Part of `/weekly-review` follow-up

## What I do

1. **Read the target anchor** (or all anchors with `--all`)
2. **Parse the `<!-- anchor-meta -->` block** (HTML comment, invisible in Obsidian) for:
   - `search-terms:` — explicit grep seeds (comma or newline separated)
   - `exclude-files:` — files the user previously rejected as false positives (ignored in future scans)
   
   If the block is missing, I fall back to inferring seeds from title tokens + frontmatter tags + section headers. I also offer to create the block after the first run.
3. **Re-scan the vault** for notes matching those terms (same grep logic as `/create-anchor`)
4. **Filter out** anything listed in `exclude-files`
5. **Build 3 lists:**
   - 🆕 **New** — vault hits that are NOT linked in the anchor AND NOT in exclude-files
   - 💔 **Broken** — links in the anchor that no longer resolve (filename does not exist anywhere in the vault)
   - 🔇 **Silent** — child links still valid (count only, no detail unless `--verbose`)
5. **Classify "New" hits** into the existing sub-topic sections (Fundamentals / Applied / Incidents / etc.) by folder heuristics:
   - `B03 Resources/studies/.../fundamentals/` → Fundamentals
   - `B01 Projects/...` → Applied
   - `B04 Archives/...` → Archive / historical
   - Unclassified → flagged for you to place
6. **Propose a diff** with the proposed edits, **only inside the child-note list**. Everything else in the anchor is untouched:
   - ✋ Thesis — not touched
   - ✋ Open questions — not touched
   - ✋ External references — not touched
   - ✋ Related anchors — not touched
   - ✋ History — appended to, never rewritten
7. **Ask for confirmation** before applying the diff
8. After approval: **apply the edits** and append to the `History` section:
   ```
   - YYYY-MM-DD: refreshed by /refresh-anchor — added N, removed M
   ```
9. **Log the run** in `C04/03 Logs & Memory/anchor-refresh-YYYY-MM-DD.md`

## Rules

- **I never rewrite authored sections.** Thesis, open questions, external references, related anchors: off-limits.
- **I never invent links.** Only link notes that exist in the vault today.
- **Broken-link removal requires explicit approval per entry** (never batch-delete in one go).
- **History is append-only.** I never edit past history entries.
- **If the anchor has no child notes at all**, the skill behaves like `/create-anchor` on it — populates from scratch.
- **Rejections are persistent.** When you reject a "New" candidate as a false positive, I append its path to the `exclude-files` list in the anchor-meta block. Future runs skip it silently. You can edit the block by hand to un-exclude.

## Variations

- `/refresh-anchor <topic>` — single anchor
- `/refresh-anchor --all` — every anchor in `A02 Anchor Topics/`
- `/refresh-anchor --stale <duration>` — only anchors not modified within the duration (e.g., `--stale 3m`, `--stale 1y`)
- `/refresh-anchor <topic> --dry-run` — report only, apply nothing
- `/refresh-anchor <topic> --verbose` — include Silent list in the report

## Interaction example

```
you: /refresh-anchor Observability
me:  Scanning vault for: observability, logging, tracing, telemetry, monitoring, datadog, grafana...
     
     Current anchor: A02 Anchor Topics/Observability.md (4 child notes)
     
     🆕 New (2):
       → Applied (projects)
         - B01 Projects/blindpay/ledger/observability-dashboard.md (created 2026-03-12)
       → Fundamentals (unclassified — please place)
         - B03 Resources/studies/Development/Backend/logs-vs-metrics.md (created 2026-02-28)
     
     💔 Broken (1):
         - [[observability plan]] — no file found with this exact filename
           Similar: 'observability-plan.md' in B01/abacatepay/ (rename?)
     
     🔇 Silent: 3 child links still valid.
     
     Proposed diff:
     ```
     + ### Applied (projects)
     + - [[observability-dashboard]] — blindpay ledger
       - [[observability plan]] → [[observability-plan]]   ← rename
       - [[observability quick starter]]
       - [[observability logs]]
     + 
     + ### Unclassified — please move to a section
     + - [[logs-vs-metrics]] — fundamentals candidate
     ```
     
     Apply? (y / edit / abort / per-change)
```

## When to split an anchor instead of refreshing

If the refresh keeps pushing the same anchor to 20+ child notes, it's a signal to split:
- Too broad → split into 2 anchors (e.g., `Observability — Logging` + `Observability — Tracing`)
- I will **suggest** a split, but never do it unilaterally

## Edge cases

- **Notes with hash-suffixed filenames** (Notion export leftovers): I treat the full name as the filename — wiki-links to these are fragile but functional.
- **Renamed files** (mv without updating links): I try to match by content similarity when the filename is off by <25%. Flagged with `Similar:` in the report.
- **False-positive seed matches** (e.g., "vim" = Portuguese verb): I always show a 1-line context snippet with each New hit so you can reject false positives. Rejections are recorded in `exclude-files`.

## Anchor-meta block format

Placed right after the frontmatter, invisible in Obsidian rendered view:

```markdown
<!-- anchor-meta
search-terms: term1, term2, term3
exclude-files:
  - path/to/rejected-file-1.md
  - path/to/rejected-file-2.md
-->
```

- `search-terms` — comma-separated or newline-separated
- `exclude-files` — one per line, relative to vault root
- Either field can be empty — the block is still valid
- If the block doesn't exist, `/refresh-anchor` offers to create it after the first run
