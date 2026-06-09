# Code Review Skill

Automated code review for Rekrut AI engineering teams. Combines deterministic security rules with LLM-powered analysis.

## When to Use
- After every engineering batch completes
- Before merging to staging
- When SEC-003 or QA-06 runs code review

## How to Use

```bash
# Review a specific file
openclaw skill run code-review --file=routes/billing.js

# Review all changed files in a commit
openclaw skill run code-review --diff=HEAD~1

# Full repository review
openclaw skill run code-review --scope=full
```

## Rulesets
- **security**: XSS, SQL injection, hardcoded secrets, NPE, thread-safety
- **performance**: N+1 queries, memory leaks, inefficient loops
- **best-practices**: Error handling, async/await, code structure

## Output Format
```
[CRITICAL] Line 45: SQL injection risk in query construction
[HIGH] Line 82: Hardcoded API key detected
[MEDIUM] Line 120: Missing error handling for async call
```

## Agent Assignment
- SEC-003: Security code review lead
- QA-006: Automated security testing
- VP-ENG: Pre-merge review gate
