---
type: project
status: active  # active | inactive | archived
created: {{date:YYYY-MM-DD}}
tags: [project]
---

# {{projectName}}

## 🎯 Objective (one sentence)

*What this project solves / delivers. Be concise.*

## 📊 Current status

**{{date:YYYY-MM-DD}}:** 
- Situation:
- Next step:
- Blockers:

## 🗓️ Timeline

- **Start:** {{date:YYYY-MM-DD}}
- **Estimated deadline:** 
- **Milestones:**
  - [ ] Milestone 1
  - [ ] Milestone 2

## 👥 People involved

- 

## 🔑 Important links

- Repo: 
- Docs: 
- Chat/channel: 

## 📋 Active tasks

- [ ] 

## 🧠 Architectural decisions

- **{{date:YYYY-MM-DD}}:** decision X because of Y
- 

## 🗃️ Suggested subfolders

```
{{projectName}}/
├── _{{projectName}}.md    (this file — index)
├── notes/                 (technical notes)
├── decisions/             (ADRs)
├── specs/                 (PRDs, specs)
└── daily/                 (project-specific daily notes)
```

## 📝 Related notes

- [[link-to-notes]]

## 🔗 Related anchors

- [[A02 Anchor Topics/...]]

---

## End of project

When closing:
1. Set `status: archived` in frontmatter
2. Write a **post-mortem** at the bottom: learnings, what worked, what could be improved
3. Optional: move folder to `B04 Archives/`

## Post-mortem (at close)

- **Outcome:** 
- **What worked:** 
- **What didn't work:** 
- **Learnings for next projects:** 
