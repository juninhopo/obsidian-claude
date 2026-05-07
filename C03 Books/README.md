# C03 Books

> **Book catalog. Metadata + reading status + rating.**

## What goes here

One entry per book:

```markdown
---
title: Atomic Habits
author: James Clear
status: finished  # want-to-read | reading | finished | abandoned
rating: 4
started: 2025-03-01
finished: 2025-04-15
tags: [productivity, habits]
isbn: 978-0735211292
---

# Atomic Habits — James Clear

## One-line summary
Change is aggregate: 1% better per day = 37x in a year.

## Why I read it
...

## Main lessons
...

## Derived notes
- [[B03 Resources/studies/productivity/atomic-habits-system]]
```

## Difference from C02 Readwise

- **C02 Readwise** = raw, auto-imported highlights
- **C03 Books** = authored book entry + status + rating

Flow: read → highlights in C02 → entry in C03 → authored notes in B03

## Dataview / Plugins

If using the **Dataview** plugin, you can run queries like:

    ```dataview
    TABLE author, rating, status
    FROM "C03 Books"
    WHERE status = "finished"
    SORT rating DESC
    ```

## Possible integrations

- **Book Search** plugin — creates an entry from ISBN/title
- **Kindle Highlights** plugin — alternative to Readwise

## Structure

Plan: one file per book, filename = title-kebab-case.md
