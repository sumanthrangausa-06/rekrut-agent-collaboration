# OpenClaw Skills Registry

Reference to the awesome-openclaw-skills repository (5,400+ skills).

## When to Use
- When an agent needs a specialized skill not in our core set
- For ad-hoc tasks (data processing, file conversion, API testing)
- When exploring new capabilities for our teams

## Key Categories for Rekrut AI

### Engineering
- `github` — PR management, issue tracking
- `code-interpreter` — Run code snippets, test functions
- `diagram-maker` — Generate architecture diagrams
- `spike` — Quick research tasks

### Security
- `healthcheck` — Service health monitoring
- `security-scanning` — Our custom security skill
- `code-review` — Our custom code review skill

### Data & Analytics
- `notion` — Documentation and wikis
- `feishu-bitable` — Data tables and tracking
- `taskflow` — Task management

### Communication
- `wecom-msg` — Enterprise messaging
- `feishu-im-read` — Read IM messages
- `content-research-writer` — Content generation

## How to Use
```bash
# Search available skills
openclaw skills list

# Check if a specific skill is available
openclaw skills check

# Run a skill
openclaw skill run github --repo=Rekrut_AI_v2 --action=issues
```

## Reference
Full registry: https://github.com/VoltAgent/awesome-openclaw-skills
