# External Skills Catalog for Rekrut AI

> Cloned from GitHub repos shared by Ranga. Updated: 2026-06-09
> Total: 8 repos, 438+ skill/agent files, 5300+ registry skills

## Repository Inventory

| Repo | Path | Agents/Skills | What It Does | When to Use |
|------|------|--------------|--------------|-------------|
| **agency-agents** | `external-skills/agency-agents/` | 275 agent files | 16 divisions of specialized AI agents (engineering, security, design, finance, etc.) | Spawning specialized agents for any task domain |
| **gstack** | `external-skills/gstack/` | 162 skills | AI engineering workflow skills (CEO review, design review, QA, ship, deploy, etc.) | Planning, review, implementation, deployment |
| **awesome-openclaw-skills** | `external-skills/awesome-openclaw-skills/` | 5300+ curated | Community-built OpenClaw skills registry | Discovering skills for any task |
| **memU** | `external-skills/memU/` | Memory service framework | Memory service with workflow-based execution, pluggable backends | When building memory/persistence features |
| **godmode** | `external-skills/godmode/` | 1 agent + hooks | Code reviewer agent with command hooks | Code review tasks |
| **code-review-graph** | `external-skills/code-review-graph/` | Code review system | Graph-based code review with beads issue tracking | Complex code review workflows |
| **open-code-review** | `external-skills/open-code-review/` | Code review automation | Automated code review for GitHub/GitLab | CI/CD code review integration |
| **defending-code-reference-harness** | `external-skills/defending-code-reference-harness/` | Security skills | Vulnerability detection and remediation | Security audits, vulnerability scanning |

---

## Key Skills for Rekrut AI CEO Work

### 1. CEO / Planning / Review (gstack)

| Skill | Command | File Path | When to Use |
|-------|---------|-----------|-------------|
| CEO Review | `/plan-ceo-review` | `gstack/.agents/skills/plan-ceo-review.md` | Review product decisions before execution |
| Engineering Review | `/plan-eng-review` | `gstack/.agents/skills/plan-eng-review.md` | Lock architecture and data flow before building |
| Design Review | `/plan-design-review` | `gstack/.agents/skills/plan-design-review.md` | Rate design dimensions 0-10 |
| AutoPlan | `/autoplan` | `gstack/.agents/skills/autoplan.md` | Run CEO → design → eng → DX review in one command |
| Spec | `/spec` | `gstack/.agents/skills/spec.md` | Turn vague intent into precise executable spec |
| Ship | `/ship` | `gstack/.agents/skills/ship.md` | Run tests, review, push, open PR |
| Land & Deploy | `/land-and-deploy` | `gstack/.agents/skills/land-and-deploy.md` | Merge PR, wait for CI, verify production health |
| Health Check | `/health` | `gstack/.agents/skills/health.md` | Code quality dashboard (types, linter, tests) |
| Benchmark | `/benchmark` | `gstack/.agents/skills/benchmark.md` | Performance regression detection |
| Security Audit | `/cso` | `gstack/.agents/skills/cso.md` | OWASP Top 10 + STRIDE security audit |
| Browser QA | `/qa` | `gstack/.agents/skills/qa.md` | Open real browser, find bugs, fix, re-verify |
| Scrape | `/scrape` | `gstack/.agents/skills/scrape.md` | Pull data from web pages |
| Retro | `/retro` | `gstack/.agents/skills/retro.md` | Weekly retro with shipping streaks |
| Context Save | `/context-save` | `gstack/.agents/skills/context-save.md` | Save working context for resumption |
| Context Restore | `/context-restore` | `gstack/.agents/skills/context-restore.md` | Resume from saved context |

### 2. Engineering Agents (agency-agents)

| Agent | File Path | Specialty | When to Use |
|-------|-----------|-----------|-------------|
| Frontend Developer | `agency-agents/engineering/engineering-frontend-developer.md` | React, UI, performance | Modern web apps, pixel-perfect UIs |
| Backend Architect | `agency-agents/engineering/engineering-backend-architect.md` | API, database, scalability | Server-side systems, microservices |
| Mobile App Builder | `agency-agents/engineering/engineering-mobile-app-builder.md` | iOS, Android, React Native | Native and cross-platform apps |
| AI Engineer | `agency-agents/engineering/engineering-ai-engineer.md` | ML models, AI integration | Machine learning features |
| DevOps Automator | `agency-agents/engineering/engineering-devops-automator.md` | CI/CD, infrastructure | Pipeline development, monitoring |
| Security Engineer | `agency-agents/security/security-application-security-engineer.md` | Security audit, hardening | Security audits, compliance |
| Database Optimizer | `agency-agents/engineering/engineering-database-optimizer.md` | Schema, queries, performance | Database performance |
| Code Reviewer | `agency-agents/engineering/engineering-code-reviewer.md` | Code review, PR checks | Reviewing code changes |
| QA Specialist | `agency-agents/engineering/engineering-model-qa-specialist.md` | E2E tests, Playwright | Testing and automation |
| Incident Responder | `agency-agents/security/security-incident-responder.md` | Incident management | Security incidents, outages |

### 3. Security / Compliance (defending-code-reference-harness + gstack)

| Skill | File Path | When to Use |
|-------|-----------|-------------|
| Vulnerability Scan | `defending-code-reference-harness/.claude/skills/vuln-scan.md` | Find security vulnerabilities |
| Threat Model | `defending-code-reference-harness/.claude/skills/threat-model.md` | Analyze threat landscape |
| Triage | `defending-code-reference-harness/.claude/skills/triage.md` | Triage security findings |
| Patch | `defending-code-reference-harness/.claude/skills/patch.md` | Fix vulnerabilities |
| Security Audit (gstack) | `gstack/.agents/skills/cso.md` | OWASP Top 10 + STRIDE |

### 4. Memory / Persistence (memU)

| Component | File Path | When to Use |
|-----------|-----------|-------------|
| Memory Service | `memU/src/memu/app/service.py` | Building memory features |
| Memorize Flow | `memU/src/memu/app/memorize.py` | Storing information |
| Retrieve Flow | `memU/src/memu/app/retrieve.py` | Retrieving information |
| Workflow Engine | `memU/src/memu/workflow/` | Building workflow-based features |

### 5. Code Review (code-review-graph + open-code-review + godmode)

| Tool | File Path | When to Use |
|------|-----------|-------------|
| Code Review Graph | `code-review-graph/` | Graph-based code review |
| Open Code Review | `open-code-review/skills/open-code-review/SKILL.md` | Automated PR review |
| Godmode Code Reviewer | `godmode/agents/code-reviewer.md` | Single-file code review |

---

## How to Use These Skills

### For Agent Spawns (sessions_spawn)

```javascript
sessions_spawn({
  agentId: "frontend-developer",
  task: "You are a frontend developer for Rekrut AI.\n\n" +
    "**BEFORE STARTING:** Read the skill file at external-skills/gstack/.agents/skills/qa.md " +
    "for guidance on browser-based QA testing.\n\n" +
    "Also read external-skills/agency-agents/engineering/engineering-frontend-developer.md " +
    "for your role and deliverables.\n\n" +
    "**WORK TO DO:** [specific task]\n\n" +
    "**Location:** /root/.openclaw/workspace/Rekrut_AI_v2/",
  taskName: "frontend-task"
})
```

### For CEO Planning (Suga's own work)

```javascript
// Before any major decision, read:
read("external-skills/gstack/.agents/skills/plan-ceo-review.md")
read("external-skills/gstack/.agents/skills/autoplan.md")

// Before deployment:
read("external-skills/gstack/.agents/skills/ship.md")
read("external-skills/gstack/.agents/skills/land-and-deploy.md")
read("external-skills/gstack/.agents/skills/health.md")

// For security review:
read("external-skills/gstack/.agents/skills/cso.md")
read("external-skills/defending-code-reference-harness/.claude/skills/vuln-scan.md")
```

### For Weekly Retros

```javascript
read("external-skills/gstack/.agents/skills/retro.md")
```

---

## Honest Assessment: What I Actually Have

**Claimed:** 210 agents
**Reality:** 
- `agency-agents` repo: 275 agent files (verified by `find . -name "*.md" | wc -l`)
- My own `agents_list` output: was truncated, I can't verify the exact count without re-running
- These are NOT "skills" in the OpenClaw sense — they're agent prompt files, not SKILL.md files

**What I should do:**
1. Reference these external repos when spawning agents (not just internal skills)
2. Read the relevant gstack/agency-agents file BEFORE delegating
3. Use gstack's `/autoplan`, `/ship`, `/health` for my own CEO orchestration
4. Stop claiming numbers I can't verify

---

## Updated Hard Rule

**Every agent spawn MUST include:**
1. Internal skill reference (from `/usr/lib/node_modules/openclaw/skills/` or `~/.openclaw/skills/`)
2. External skill reference (from `external-skills/` repos) when relevant
3. Agent must read BOTH before starting work

**For CEO autonomous work, I MUST read:**
- `external-skills/gstack/.agents/skills/autoplan.md` before planning
- `external-skills/gstack/.agents/skills/ship.md` before deploying
- `external-skills/gstack/.agents/skills/health.md` for monitoring
- `external-skills/gstack/.agents/skills/retro.md` for weekly reviews
