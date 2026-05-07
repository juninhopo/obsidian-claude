---
name: search-books
description: Query the C03 Books catalog by title, author, tag, status, or rating
invocation: "search books <query>" | "/search-books <query>"
---

# Skill: search-books

## When to use

- You remember reading a book but forgot the title
- You want to list books by status (finished / reading / want-to-read)
- You need to reference a book in a note and want the exact metadata

## What I do

1. **Parse the query:**
   - Plain text → matches title, author, one-line summary
   - `--status <state>` → filter by reading status
   - `--tag <tag>` → filter by tag in frontmatter
   - `--rating >=N` → filter by minimum rating
   - `--author <name>` → exact author match
2. **Read every `.md` file** in `C03 Books/` (excluding README)
3. **Parse frontmatter** from each entry
4. **Apply filters** and sort:
   - Default: rating DESC, then finished DESC
   - `--sort date` → by finished date DESC
5. **Return a table:**

   ```
   === BOOKS matching "atomic" ===
   | Title          | Author       | Status   | Rating | Finished   |
   |----------------|--------------|----------|--------|------------|
   | Atomic Habits  | James Clear  | finished | 4      | 2025-04-15 |
   ```
6. **Offer next actions:**
   - Open a specific entry
   - Show derived notes (linked from that book)
   - Count-only summary for large results

## Rules

- **Read-only.** I never modify C03 entries.
- **Graceful on missing frontmatter.** If an entry has no frontmatter, I show `(no metadata)` and return what I can from the body.
- **No external API calls.** Works only against the local catalog.

## Variations

- `/search-books <query>` — free text
- `/search-books --status finished --rating >=4` — best books I've read
- `/search-books --tag productivity` — by tag
- `/search-books --author "James Clear"` — by author
- `/search-books --want-to-read` — shortcut for `--status want-to-read`

## Interaction example

```
you: /search-books --status finished --rating >=4
me:  Found 7 books.
     
     | Title           | Author        | Rating | Finished   |
     |-----------------|---------------|--------|------------|
     | Atomic Habits   | James Clear   | 5      | 2025-04-15 |
     | Deep Work       | Cal Newport   | 5      | 2024-11-02 |
     | Antifragile     | N. Taleb      | 4      | 2024-08-20 |
     ...
     
     Next? (open <title> / derived-notes / count-by-tag)
```

## Dependencies

Works best if you use the template suggested in `C03 Books/README.md` (YAML frontmatter with title/author/status/rating/tags).
