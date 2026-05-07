# A03 Attachments

> **Central repository for images and attachments across the vault.**

## What goes here

- Screenshots
- Photos attached to notes
- Referenced PDFs
- Diagram images (non-Excalidraw — those go in C05)
- Any binary attachment referenced by notes

## How to reference

Obsidian resolves links by filename, not path. So from any note you can write:

```markdown
![[Screenshot 2026-04-23.png]]
```

It does not matter that the note is in B01/blindpay and the image is in A03 — the link works.

## Obsidian configuration

Configure Obsidian to save attachments directly here:
1. Settings → Files & Links → Default location for new attachments
2. Choose: **In the folder specified below**
3. Path: `A03 Attachments`

## Naming convention

- **Automatic screenshots:** keep `Screenshot YYYY-MM-DD at HH.MM.SS.png` (macOS default)
- **Manually created images:** kebab-case — `architecture-diagram.png`
- **Code screenshots:** `code-` prefix — `code-error-stripe.png`

## Cleanup

Run `/orphan-images` (if that skill is created) to find orphan images (not referenced by any note).

## Excalidraw ≠ image

`.excalidraw.md` drawings go in **C05 Excalidraw**. Only PNGs/JPGs/PDFs here.
