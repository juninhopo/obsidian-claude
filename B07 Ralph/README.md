# B07 Ralph

> **My personal local agent — runs on my Mac, executes things I ask via chat.**

## What is Ralph

Ralph is a personal autonomous-execution agent on the user's Mac. It listens to messages (via Telegram), spawns Claude Code in headless mode (`claude -p`) with full local tools, and reports the result back to chat. Configs live in this vault folder; runtime code lives at `~/Developer/ralph/`; secrets live at `~/.local-ralph/`.

This is **not** the Capibara Ralph (autonomous code agent via GitHub Actions for the Capibara SaaS). Same name, different scope: this Ralph is local, personal, and broader (any task, not just code).

## Folder structure

```
B07 Ralph/                         ← this folder (configs in vault)
├── README.md                       ← this file
├── IDENTITY.md                     ← Ralph's identity (name, vibe, emoji)
├── SOUL.md                         ← behavior rules + red lines
├── USER.md                         ← who Darlan is, what he wants
├── TOOLS.md                        ← which Claude Code tools are allowed
└── runs/                           ← markdown log of every task (date-time-slug.md)
    └── YYYY-MM-DD/
        └── HHMM-<slug>.md
```

```
~/Developer/ralph/                 ← runtime code (not in vault)
├── package.json
├── src/
│   ├── index.ts                    ← entry point: Telegram poller + dispatcher
│   ├── executor.ts                 ← spawns `claude -p` with task + persona
│   └── config.ts                   ← reads vault configs + env vars
├── README.md
├── .env.example
└── .gitignore
```

```
~/.local-ralph/                    ← secrets (NOT in vault)
├── config.json                     ← TELEGRAM_BOT_TOKEN, AUTHORIZED_CHAT_IDS
└── queue/                          ← in-flight task state (json files)
```

## How it works

```
You (Telegram message)
    ↓
Ralph daemon (~/Developer/ralph/) polls Telegram
    ↓
Reads vault configs (IDENTITY/SOUL/USER/TOOLS) + appends to system prompt
    ↓
spawn: claude -p "<task>" --allowedTools "<from TOOLS.md>" --model opus[1m]
    ↓
Streams output back to Telegram + writes log to B07 Ralph/runs/YYYY-MM-DD/HHMM-<slug>.md
```

## Setup checklist

1. [ ] Create a new Telegram bot via [@BotFather](https://t.me/BotFather) — save the token
2. [ ] Get your own Telegram chat ID: send `/start` to [@userinfobot](https://t.me/userinfobot) — save the chat ID
3. [ ] Fill `~/.local-ralph/config.json` with `TELEGRAM_BOT_TOKEN` and `AUTHORIZED_CHAT_IDS: [<your-id>]`
4. [ ] Review `IDENTITY.md`, `SOUL.md`, `USER.md`, `TOOLS.md` — adjust to taste
5. [ ] Run the daemon: `cd ~/Developer/ralph && bun run dev`
6. [ ] Send a test message to your bot — Ralph should reply

## Files NOT in the vault

These stay outside the vault (volatile or sensitive):

- `~/.local-ralph/config.json` — Telegram bot token, authorized chat IDs
- `~/.local-ralph/queue/` — in-flight tasks
- `~/Developer/ralph/.env` — env vars
- `~/Developer/ralph/node_modules/`

## Editing configs

Edit `IDENTITY.md`, `SOUL.md`, `USER.md`, `TOOLS.md` directly in Obsidian. Ralph re-reads them on every task (no daemon restart needed).

`runs/YYYY-MM-DD/` is written by Ralph — don't hand-edit. Browse to see history.

## Rule

**Verbatim** when Ralph writes here. If Ralph produces content (run logs, summaries), don't tighten or rewrite. The agent is its own author for those files.

User-authored markdown (IDENTITY, SOUL, USER, TOOLS) follows the standard authorship rule — user writes, AI suggests as diff.

## Setup history

- 2026-04-28 — initial scaffold: created `B07 Ralph/` (configs), `~/Developer/ralph/` (skeleton runtime), `~/.local-ralph/` (secrets dir). Replaces the deleted OpenClaw setup. Telegram bot creation pending (see Setup checklist).

## Cross-references

- Runtime code: `~/Developer/ralph/`
- Secrets: `~/.local-ralph/config.json`
- Replaces: `B04 Archives/old-projects/openclaw/` (previous attempt, archived 2026-04-28)
