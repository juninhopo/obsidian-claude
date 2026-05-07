---
name: weekly-review
description: Weekly vault review — checks system health and proposes maintenance
invocation: "weekly review" | "/weekly-review"
---

# Skill: weekly-review

## When to use

- Every Friday (or Sunday) — pick one and be consistent
- End of the month for a deeper review

## Checklist I run

### 1. A00 Inbox — triage
- [ ] How many items?
- [ ] Any older than 7 days? (flag)
- [ ] Run `/process-inbox`?

### 2. A01 Processing — promotion
- [ ] Notes sitting there for 30+ days → promote or archive
- [ ] Propose promotions to A02/B03

### 3. A04 Daily Notes — consolidation
- [ ] Daily notes from the week: anything worth extracting?
- [ ] Recurring ideas that could become anchor candidates?

### 4. A05 Backlog — pruning
- [ ] Items older than 1 year → decide (promote or archive)

### 5. B01 Projects — health check
- [ ] Which projects had no update in 30 days? → status?
- [ ] Any active project without a clear next step?

### 6. Broken links
- [ ] Run the broken-links script
- [ ] Report new broken links (don't auto-fix)

### 7. Duplicates
- [ ] Scan for files with similar names
- [ ] Flag merge candidates

### 8. Output: weekly report

Saved to `B05 Systems/weekly-reviews/YYYY-MM-DD.md`:

```markdown
# Weekly Review — YYYY-MM-DD

## Vault state
- Total notes: X
- Inbox: Y items (Z older than 7 days)
- Broken links: N

## Proposed actions
- [ ] ...

## Suggested promotions
- A01/<note> → B03 (reason: ...)

## Flags
- 🚩 <project> no update in 45 days

## What changed since last review
- ...
```

## Variations

- `/weekly-review light` — flags only, no proposals
- `/weekly-review deep` — with link analysis and duplicates

## Automation

Schedule with Claude Code `/loop`:
```
/loop 7d /weekly-review
```

Or via `cron` on the harness config.
