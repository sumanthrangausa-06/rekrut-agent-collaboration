# Memory Management Skill

Structured memory system for Rekrut AI CEO and all agents. 10x token reduction through intent capture and structured storage.

## When to Use
- Every agent session start (load context)
- Every agent session end (save learnings)
- CEO daily reviews (consolidate agent memories)
- When context compression is needed

## Memory Types
| Type | Content | Example |
|------|---------|---------|
| Episodic | Events, sessions | "June 7: Deployed SPA auth fix" |
| Semantic | Facts, knowledge | "User prefers PST timezone" |
| Procedural | How-to, processes | "Deploy: git push → Render auto-deploy" |

## How to Use

```bash
# Save agent memory
openclaw skill run memory-management --save --agent=FE-004 --type=episodic --content="Fixed responsive grid"

# Load relevant context
openclaw skill run memory-management --query --agent=FE-004 --task="responsive design"

# Consolidate daily memories
openclaw skill run memory-management --consolidate --date=2026-06-07
```

## For CEO
- Consolidate all agent memories into daily summary
- Track which agents worked on what
- Identify patterns across teams
- Build long-term company knowledge base
