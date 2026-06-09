# agent-orchestrator

Kanban-style dashboard and agent task management for Rekrut AI.

## Dashboard

Access at: `/admin/agent-dashboard`

### Columns

| Column | Status | Purpose |
|--------|--------|---------|
| To Do | `todo` | Tasks queued but not started |
| In Progress | `in-progress` | Active agent work |
| Review | `review` | Pending code review / QA |
| Done | `done` | Completed and verified |
| Blocked | `blocked` | Stuck, needs intervention |
| Timeout | `timeout` | Agent timed out, needs retry |

### Task Card Fields

```typescript
interface AgentTask {
  id: string
  agentName: string      // e.g., 'frontend-developer'
  agentRole: string      // e.g., 'Frontend Developer'
  task: string           // Short title
  description: string    // What to do
  status: 'todo' | 'in-progress' | 'review' | 'done' | 'blocked' | 'timeout'
  priority: 'low' | 'medium' | 'high' | 'critical'
  progress: number       // 0-100
  startedAt?: string
  completedAt?: string
  estimatedDuration: string  // e.g., "15m"
  files: string[]        // Files to modify
  subagentId?: string    // Spawned session ID
  sessionKey?: string   // OpenClaw session key
  error?: string         // Blockage reason
  notes: string[]        // Progress log
}
```

## Agent Registry

| Agent | Role | Timeout | Best For |
|-------|------|---------|----------|
| frontend-developer | Frontend Dev | 3m | React components, CSS, TypeScript fixes |
| ui-designer | UI Designer | 3m | Mockups, SVG, design systems |
| backend-developer | Backend Dev | 5m | API routes, DB schema, Supabase |
| qa-engineer | QA Engineer | 5m | Playwright tests, manual testing |
| security-auditor | Security Officer | 3m | OWASP audit, threat modeling |
| accessibility-auditor | A11y Auditor | 3m | ARIA labels, WCAG compliance |
| content-strategist | Content Writer | 3m | Copy, email templates, docs |
| devops-engineer | DevOps | 5m | Deploy pipelines, CI/CD, infra |
| data-scientist | ML Engineer | 5m | AI models, embeddings, scoring |
| product-owner | Product Owner | 5m | Requirements, user stories, prioritization |

## Task Assignment Rules

### 1. Micro-Task Principle

**❌ Bad:** "Fix all TypeScript errors across the project" (50+ files, always times out)

**✅ Good:** "Fix Select component in `applications.tsx`" (1 file, 1 component, fits in 3m)

### 2. Timeout Strategy

| Task Complexity | Max Files | Timeout | Approach |
|-----------------|-----------|---------|----------|
| Simple | 1 | 3m | Direct edit |
| Medium | 2-3 | 3m | Create new file + minor edit |
| Complex | 5+ | 5m | Break into sub-tasks |
| Architecture | 10+ | 10m | Plan first, then execute in chunks |

### 3. File Size Threshold

- < 200 lines: Agent can read + edit in 1m
- 200-500 lines: Agent reads in 1m, needs 2m for edits
- 500-1000 lines: Agent reads in 2m, rarely finishes edits
- 1000+ lines: **Always break into smaller tasks**

### 4. Escalation Path

```
Agent times out → Retry once with smaller scope
              → Still fails → Escalate to human (me)
              → Human fixes → Document pattern for future
```

## Workflow Integration

### Morning Standup (automated)

1. Check `blocked` and `timeout` columns
2. Reassign failed tasks with smaller scope
3. Move `review` tasks to `done` if approved
4. Queue new `todo` items based on sprint goals

### Continuous Integration

1. When task moves to `done`, run `code-review-graph detect-changes --brief`
2. Validate impact radius matches expected files
3. Run `npm run build` to verify TypeScript
4. Auto-move to `review` if build passes

### Sprint Planning

1. Load tasks from `gstack-workflow` /office-hours output
2. Assign to agents based on role + availability
3. Set estimated duration using historical data
4. Prioritize by `critical` → `high` → `medium` → `low`

## Commands

```bash
# Add new task to board
# (Manual via dashboard UI, or automated via API)

# Get current agent status
curl /api/admin/agent-status

# Retry failed task
POST /api/admin/tasks/{id}/retry

# Reassign task
POST /api/admin/tasks/{id}/reassign
body: { agentName: 'frontend-developer' }
```

## Integration with gstack-workflow

```
/office-hours → Creates tasks → Dashboard shows in To Do
/plan-eng-review → Adds technical specs → Task description updated
/review → Moves task to Review → QA picks up
/qa → Moves to Done or Blocked → Auto-generates regression tests
/ship → Archive completed tasks → Sprint retro data
```

## Patterns

### Pattern 1: Feature Development

```
1. Product Owner: /office-hours → creates task
2. UI Designer: /design-shotgun → generates mockups
3. Frontend Dev: /plan-eng-review → implements
4. Code Reviewer: /review → catches issues
5. QA: /qa → tests flows
6. DevOps: /ship → deploys
```

### Pattern 2: Bug Fix

```
1. QA: /investigate → traces root cause
2. Frontend Dev: Fix → single file, single bug
3. QA: Re-test → verify fix + regression test
4. Code Reviewer: /review → verify no side effects
```

### Pattern 3: Refactor

```
1. Staff Engineer: /plan-eng-review → migration plan
2. Code Review Graph: get_impact_radius → affected files
3. Frontend Dev: Per-file refactor tasks (micro-tasks)
4. Code Reviewer: /review → verify each file
5. QA: /qa → full regression test
```

## Metrics

Track per agent:
- Tasks completed / timed out / blocked
- Average duration vs estimated
- Token savings from code-review-graph
- Files touched per task
- Build success rate

## Links

- gstack-workflow: `/root/.openclaw/workspace/skills/gstack-workflow/SKILL.md`
- code-review-graph: `/root/.openclaw/workspace/skills/code-review-graph/SKILL.md`
- Dashboard page: `/root/.openclaw/workspace/Rekrut_AI_v2/client/src/pages/admin/agent-dashboard.tsx`
