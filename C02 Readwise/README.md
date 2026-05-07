# C02 Readwise

> **Readwise integration. Reading highlights land here automatically.**

## What is Readwise

Readwise captures highlights from Kindle, Pocket, web articles, podcasts, etc. and syncs with Obsidian via plugin.

## Setup

1. Install the **Readwise Official** plugin in Obsidian (Community Plugins)
2. Settings → Readwise Official → sign in
3. Settings → Readwise Official → Sync destination: `C02 Readwise`
4. (Optional) configure highlight template in C04/06

## Flow

```
Kindle/Pocket → Readwise → Obsidian (C02) → review → B03 Resources
```

## Rule

Highlights in C02 are raw material. Not your thinking. To become knowledge:

1. Read the highlight
2. Decide: is this worth anything?
3. If yes: create an authored note in B03 with YOUR reading (do not paste verbatim)
4. Link to an anchor in A02 if it fits

## Typical structure

```
C02 Readwise/
├── Books/
│   └── Atomic Habits - James Clear.md
├── Articles/
└── Podcasts/
```

(The plugin creates the structure automatically)

## What NOT to do

- Edit files in C02 (the plugin will overwrite them on the next sync)
- Create anchors/MOCs here (those go in A02)

## When the plugin is not active

This folder stays empty. It's a placeholder for when you decide to activate it.
