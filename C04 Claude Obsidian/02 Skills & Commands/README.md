# 02 Skills & Commands

> **Named workflows you can invoke.**

## Available skills

### Daily / Capture
| Skill | What it does | When to use |
|-------|--------------|-------------|
| `/create-daily` | Create today's daily, carry over yesterday's unfinished tasks, populate Calendar meetings | Start of the day |
| `/calendar-week` | Show the upcoming week's meetings + free blocks | Monday planning, before scheduling |
| `/process-inbox` | Triage A00 Inbox | Weekly, or when A00 has 10+ items |
| `/process-meetings` | Import new meetings from Granola into B06 | After meetings, or weekly bulk |

### Knowledge / Structure
| Skill | What it does | When to use |
|-------|--------------|-------------|
| `/create-anchor <topic>` | Create an MOC in A02 | Recurring topic without an entry point |
| `/refresh-anchor <topic>` | Audit an anchor against the vault and propose updates | Quarterly review, after reorgs, when anchor feels stale |
| `/promote-to-b03 <note>` | Move mature A01 note to B03 with checklist | Note matured and needs permanent home |
| `/link-suggestions <note>` | Propose wiki-links for a note | After writing or promoting a note |

### Projects / Archive
| Skill | What it does | When to use |
|-------|--------------|-------------|
| `/create-project <name>` | Scaffold a new project in B01 | Starting a project >1 week, ≥3 notes |
| `/archive-project <project>` | Move project to B04 with post-mortem | Project closed, no updates in 60+ days |
| `/project-recap <name>` | Weekly recap of a project (done, in-progress, blocked, task tree per day) | Before standup, end of sprint, weekly meeting |

### Maintenance
| Skill | What it does | When to use |
|-------|--------------|-------------|
| `/weekly-review` | Weekly vault review | Every Friday |
| `/orphan-images` | Find unreferenced images in A03 | Monthly cleanup |
| `/sync-shared` | Sync build (skills, templates, READMEs, CLAUDE.md) into `_shared/` kit | After updating any build file you want shareable |

### Search
| Skill | What it does | When to use |
|-------|--------------|-------------|
| `/search-books <query>` | Query the C03 Books catalog | Lookup by title/author/tag/status |

## How to invoke

Inside Claude Code:
- `/process-inbox` — if registered as a slash command
- Or in natural language: "process my inbox", "run weekly review"

## How to create a new skill

1. Create a `.md` file in this folder
2. Minimum frontmatter:
   ```yaml
   ---
   name: skill-name
   description: what the skill does
   invocation: "natural command" | "/slash-command"
   ---
   ```
3. Skill body: **precise instructions** for what I (Claude) should do
4. Include:
   - When to use
   - Steps (numbered)
   - Variations
   - Interaction examples

## Good skill vs. bad skill

**Good** — has an explicit prompt ("read each file, classify by this taxonomy, report...")
**Bad** — generic ("do whatever you think is best")

I (Claude) execute skills literally. A bad skill = bad results.

## Automation

To run skills automatically:
- `/loop <time> /<skill>` — Claude Code loop
- Personal cron (outside Claude)
- Claude Code hooks in `.claude/settings.json`

## Future skill candidates

Ideas not yet implemented:
- `/consolidate-daily-notes <range>` — extract recurring themes from a range of daily notes
- `/graph-health` — report clusters of disconnected notes
- `/promote-to-anchor <note>` — upgrade an A01 note directly to a fully structured A02 anchor
- `/tag-audit` — find inconsistent or duplicate tags across the vault
