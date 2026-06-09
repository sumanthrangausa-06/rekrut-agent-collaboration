# gstack-workflow

Garry Tan's product development workflow adapted for Rekrut AI agent orchestration.

**Reference:** https://github.com/garrytan/gstack

## Philosophy

Think → Plan → Build → Review → Test → Ship → Reflect

Each skill feeds into the next. Nothing falls through cracks because every step knows what came before.

## Workflow Skills

### 1. /office-hours

**Role:** Product Owner / CEO

Before writing code, interrogate the product idea with 6 forcing questions:

1. What pain are you solving? (Specific examples, not hypotheticals)
2. Who feels this pain most acutely? (Be specific — "engineers at 50-person startups")
3. What are they doing right now to solve it? (Workarounds, hacks, manual processes)
4. Why isn't that good enough? (Where does it break?)
5. What would a 10-star solution look like? (Ignore constraints, dream)
6. What's the narrowest wedge that delivers value tomorrow? (Ship today, iterate)

**Output:** Design document that feeds into every downstream skill.

### 2. /plan-ceo-review

**Role:** Strategic Review

Four scope modes:
- **Expansion:** "What if we 10x this?"
- **Selective Expansion:** "Which 2 features would double impact?"
- **Hold Scope:** "This is exactly right. Don't add anything."
- **Reduction:** "What can we cut and still ship value?"

**Output:** Approved scope with boundaries.

### 3. /plan-eng-review

**Role:** Engineering Manager

Lock in architecture before code:
- ASCII data flow diagrams
- State machine for user flows
- Error paths (what goes wrong, what we show)
- Test matrix (what we test, how we verify)
- Security concerns (what data touches what, where it's stored)

**Output:** Technical specification with diagrams.

### 4. /plan-design-review

**Role:** Senior Designer

Rate each design dimension 0-10:
- Visual hierarchy
- Information density
- Interaction clarity
- Accessibility
- Mobile experience
- Performance perception

Explain what a 10 looks like. Then edit the plan to get there.

**Output:** Design criteria with reference points.

### 5. /review

**Role:** Staff Engineer

Find bugs that pass CI but blow up in production:
- Race conditions
- Missing error handling
- Unvalidated inputs
- State inconsistency
- API contract mismatches

Auto-fix obvious issues. Flag completeness gaps.

**Output:** Review report with fixes or tickets.

### 6. /qa

**Role:** QA Lead

Test the actual app, not the code:
- Click through every flow
- Try edge cases (empty state, 1000 items, slow network)
- Screenshot before/after
- Generate regression tests for every bug found

**Output:** Bug report with reproduction steps + tests.

### 7. /ship

**Role:** Release Engineer

- Sync main
- Run tests
- Audit coverage
- Push to branch
- Open PR with description
- Verify CI passes

**Output:** Clean PR ready for merge.

### 8. /cso

**Role:** Chief Security Officer

OWASP Top 10 + STRIDE threat model:
- Injection vulnerabilities
- Authentication flaws
- Sensitive data exposure
- XXE, broken access control
- Security misconfiguration

Each finding includes concrete exploit scenario.

**Output:** Security audit report.

## Agent Assignment Rules

| Stage | Agent | Timeout | Task Size |
|-------|-------|---------|-----------|
| Product | CEO/Product Owner | 5m | 1 feature idea |
| Design | UI Designer | 3m | 1 mockup variant |
| Engineering | Frontend/Backend Dev | 3m | 1 file modification |
| Review | Code Reviewer | 3m | 1 component review |
| QA | QA Engineer | 5m | 1 flow test |
| Security | Security Auditor | 3m | 1 threat surface |
| Ship | DevOps | 5m | 1 deploy pipeline |

## Golden Rule

**One agent = one file, one task.**

Multi-file tasks always timeout. Break them into micro-tasks.

## Checkpointing

After each skill completes, write a checkpoint:
- What decisions were made
- What remains
- Failed approaches

This survives crashes and context switches.

## Cross-Model Review

When possible, run /review and /codex (different model) on same code. Cross-model analysis catches what single model misses.

## Links

- Full gstack docs: https://github.com/garrytan/gstack/blob/main/docs/skills.md
- OpenClaw integration: https://github.com/garrytan/gstack/blob/main/docs/OPENCLAW.md
- Karpathy rules: https://github.com/forrestchang/andrej-karpathy-skills
