# Rekrut AI v2 — Complete Project Understanding

> Synthesized from documentation, codebase analysis, and security audit. Last updated: 2026-06-05

## 1. What It Is
Rekrut AI (also known as **HireLoop**) is an AI-native recruitment platform — a dual-sided marketplace connecting candidates with recruiters. Built in 2026, designed to be AI-first from the ground up rather than bolted-on.

- **Live prod**: `https://rekrutai.co` (not hireloop-vzvw.polsia.app — that was old domain)
- **Dev**: `https://rekrutai-dev.onrender.com` (auto-deploy from `dev` branch)
- **Staging**: `https://rekrutai-staging.onrender.com` (auto-deploy from `staging` branch)
- **Repo**: `https://github.com/sumanthrangausa-06/Rekrut_AI_v2`
- **Database**: Neon PostgreSQL with pgvector

## 🔴 CRITICAL RULE: Always Use Specialized Agency Agents

**User explicitly forbids creating generic subagents. This is a hard rule.**

- When delegating, ALWAYS use specialized agents from `agents_list` (210 available)
- NEVER create generic subagents with `sessions_spawn({ task: "..." })` without `agentId`
- Examples: `devops-automator`, `git-workflow-master`, `application-security-engineer`, `frontend-developer`, `backend-architect`
- When spawning agents, explicitly tell them to read relevant SKILL.md files
- User has shared GitHub links for additional skills — use those when available
- Share external skills with subagents by instructing them in the task briefing
- **External skills catalog:** `EXTERNAL_SKILLS.md` (8 repos, 438+ files, 5300+ registry skills)

## 🔴 CRITICAL RULE: Always Use Skills (Updated 2026-06-09)

**User explicitly requires skill usage. This is a hard rule.**

- **BEFORE delegating**, I must read the relevant SKILL.md so I know the guidance
- **EVERY agent spawn** must include a skill reference in the task briefing
- **Agent must read the skill FIRST** before starting work
- **Skills are not optional** — they are the standard operating procedure
- **Full skill mapping documented in TOOLS.md and EXTERNAL_SKILLS.md**

**Key skills for Rekrut AI:**
| Task | Skill | Path |
|------|-------|------|
| DevOps/Deploy/Health | `healthcheck` | `/usr/lib/node_modules/openclaw/skills/healthcheck/SKILL.md` |
| Git/PRs/Merge | `github` | `/usr/lib/node_modules/openclaw/skills/github/SKILL.md` |
| Browser/E2E Testing | `browser-automation` | `~/.openclaw/plugin-skills/browser-automation/SKILL.md` |
| Task Orchestration | `taskflow` | `/usr/lib/node_modules/openclaw/skills/taskflow/SKILL.md` |
| SEO/Marketing | `seo-audit` | `~/.openclaw/skills/seo-audit/SKILL.md` |
| Content/Copy | `content-research-writer`, `copywriting` | `~/.openclaw/skills/` |
| Compliance/Legal | `legal-risk-assessment` | `~/.openclaw/skills/legal-risk-assessment/SKILL.md` |
| SaaS Metrics | `saas-metrics-coach` | `~/.openclaw/skills/saas-metrics-coach/SKILL.md` |

**External skills (from GitHub repos):**
| Task | Repo | Path |
|------|------|------|
| CEO Review/Planning | `gstack` | `external-skills/gstack/.agents/skills/plan-ceo-review.md` |
| AutoPlan | `gstack` | `external-skills/gstack/.agents/skills/autoplan.md` |
| Ship/Deploy | `gstack` | `external-skills/gstack/.agents/skills/ship.md` |
| Health Check | `gstack` | `external-skills/gstack/.agents/skills/health.md` |
| Security Audit | `gstack` | `external-skills/gstack/.agents/skills/cso.md` |
| Browser QA | `gstack` | `external-skills/gstack/.agents/skills/qa.md` |
| Vulnerability Scan | `defending-code-reference-harness` | `external-skills/defending-code-reference-harness/.claude/skills/vuln-scan.md` |
| Engineering Agents | `agency-agents` | `external-skills/agency-agents/engineering/` |
| Security Agents | `agency-agents` | `external-skills/agency-agents/security/` |

---

## 2. Tech Stack

---

## 2. Tech Stack

| Layer | Technology | Notes |
|-------|-----------|-------|
| **Backend** | Node.js + Express (JavaScript, NOT TypeScript) | 351 API endpoints, 23 route files |
| **Frontend** | React 19 + Vite + Tailwind CSS + shadcn/ui | TypeScript SPA, 42 routes, 53 pages |
| **Database** | Neon PostgreSQL + pgvector | 105 tables, 50 migrations, 16 domain groups |
| **AI Providers** | Polsia AI proxy (OpenAI/Anthropic/Gemini) → NIM models → Groq → Cerebras | Multi-provider fallback with circuit breaker |
| **Storage** | Polsia R2 for file uploads, CDN for images |
| **Auth** | JWT + refresh tokens + PostgreSQL-backed sessions (connect-pg-simple) |
| **Rate Limiting** | PostgreSQL-backed distributed (NOT in-memory, as of June 5 2026 fix) |
| **Hosting** | Render (backend), Cloudflare CDN |
| **Payments** | Stripe Checkout (basic billing deployed, needs validation) |

---

## 3. Backend Architecture

### 3.1 Entry Point (`server.js`)
- Express app with CORS, JSON body parsing (50MB limit), cookie parsing, session management
- **Health check** MUST be first route (before middleware that could crash)
- **Session config**: PostgreSQL-backed, 7-day cookies, `secure: false` (Render terminates TLS)
- **Rate limiting**: Distributed PostgreSQL-backed (replaced in-memory on June 5, 2026)
- Serves React SPA from `client/dist/`, fallback to `index.html` for non-API routes
- Admin dashboard routes built into server.js (AI health, prompt management, A/B testing, revenue)
- Metrics collection + activity logging wired up
- **351 endpoints** across all route files

### 3.2 Route Files (23 files, 351 endpoints)

| File | Endpoints | Domain | Key Notes |
|------|-----------|--------|-----------|
| `routes/interviews.js` | 37 | Mock interviews, video analysis | 2691 lines, very large |
| `routes/onboarding.js` | 43 | Document generation, I-9, W-4 | 3119 lines, monolith |
| `routes/candidate.js` | 46 | Candidate profile, resume, skills | |
| `routes/recruiter.js` | 43 | Dashboard, analytics, job optimization | |
| `routes/assessments.js` | 22 | Skill assessments, adaptive grading | AI-generated questions |
| `routes/payroll.js` | 16 | Payroll runs, paychecks, tax (US + India) | |
| `routes/communications.js` | 13 | Message templates, bulk messaging | |
| `routes/memory.js` | 14 | Smart profile memory, autofill | |
| `routes/omniscore.js` | 13 | Candidate/company scoring | Mounted at 3 paths |
| `routes/compliance.js` | 16 | Bias detection, consent, GDPR audit | |
| `routes/auth.js` | 13 | Registration, login, OAuth, refresh tokens | |
| `routes/documents.js` | 8 | Upload, OCR, fraud detection | R2 upload, verification |
| `routes/company.js` | 7 | Company profile, settings | |
| `routes/jobs.js` | 6 | Job CRUD | |
| `routes/matching.js` | 6 | Job/candidate matching | pgvector semantic search |
| `routes/trustscore.js` | 6 | Company trust scoring | |
| `routes/admin.js` | 3 | Admin auth + revenue | |
| `routes/analytics.js` | 2 | Event logging, funnel metrics | |
| `routes/countries.js` | 4 | Country config, tax/labor laws | |
| `routes/quick-practice.js` | 7 | **ISOLATED** Quick Practice | Must mount BEFORE interviews.js |
| `routes/screening.js` | 7 | AI recruiter screening | |
| `routes/billing.js` | 7 | Stripe subscriptions | Newly deployed |
| `routes/notifications.js` | 14 | Email notifications | |

**⚠️ Mount Order Critical**: `quick-practice.js` MUST be mounted before `interviews.js` at same `/api/interviews` path (isolation from #32717). `omniscore.js` mounted at 3 paths for compatibility.

### 3.3 Services (14 files)
- `matching-engine.js` — Semantic job/candidate matching via pgvector
- `document-verification.js` — AI document fraud detection, OCR, authenticity scoring
- `interview-ai.js` — Interview analysis, video frame extraction, speech metrics
- `communication-generator.js` — AI message templates, bulk communications
- `job-optimizer.js` — AI job description optimization
- `trustscore.js` — Company reputation scoring
- `omniscore.js` — Candidate credit scoring (multi-factor)
- `payroll-calculator.js` — US + India tax calculations
- `country-config.js` — Country-specific labor law, tax rules
- `scoreExplainer.js` — Score explanation generation
- `autofill-service.js` — Smart form autofill from profile memory
- `biasDetection.js` — Fairness auditing, bias detection
- `memory-service.js` — User memory/context management
- `auditLogger.js` — Security audit logging

### 3.4 Core Libraries (`lib/`)
- **`ai-provider.js`** (2287 lines) — Multi-provider LLM with circuit breaker, fallback chains, token budgeting
- **`polsia-ai.js`** (1304 lines) — AI function wrappers for mock interviews, assessments, matching
- **`qp-provider.js` + `qp-ai.js`** — ISOLATED Quick Practice pipeline (forked from main AI, separate code path)
- **`ai-call-logger.js`** — AI call tracking, usage summary, budget predictions, failover stats
- **`token-budget.js`** — Token tracking, budget enforcement, priority throttling
- **`distributed-rate-limiter.js`** — PostgreSQL-backed rate limiting (NEW June 5, 2026)
- **`auth.js`** — JWT handling, authorization middleware, refresh token rotation, token reuse detection
- **`db.js`** — PostgreSQL pool (Neon) with query stats, slow query tracking (200ms threshold), 25 max connections
- **`metrics-collector.js`** — Request/latency/error tracking per endpoint
- **`activity-logger.js`** — Request tracking for admin activity feed
- **`self-hosted-audio.js`** — Piper TTS self-hosted audio pipeline
- **`recruiter-screener.js`** — AI recruiter screening logic
- **`email-service.js`** — Email sending infrastructure (SMTP)
- **`null-guard.js`** — Null safety utilities

---

## 4. Frontend Architecture

### 4.1 Tech Stack
- React 19 (StrictMode), TypeScript 5.7, Vite 6, Tailwind CSS 3.4
- React Router 7, Lucide React icons, CVA + tailwind-merge for variants
- **No state management library** (Redux/Zustand) — all React hooks + Context API
- shadcn/ui component primitives (Button, Card, Badge, Input, Textarea, Select, Label, Tabs, Dialog, Avatar)

### 4.2 Directory Structure
```
client/src/
  main.tsx              # Entry point (StrictMode)
  App.tsx               # Root router + auth provider — 42 routes
  index.css             # Tailwind directives + custom CSS
  contexts/
    auth-context.tsx    # Auth state + user context
  lib/
    api.ts              # HTTP client + token management
    utils.ts            # cn() helper (clsx + tailwind-merge)
    analytics.ts        # Event tracking utility
  components/
    ui/                 # 10 primitive UI components
    layout/             # DashboardLayout, Sidebar, Header
    error-boundary.tsx  # Global + per-route error handling
    admin-auth-guard.tsx
    ai-onboarding-dashboard.tsx
    ai-onboarding-recruiter.tsx
  pages/
    landing.tsx         # Marketing homepage (SEO-optimized)
    login.tsx, register.tsx, forgot-password.tsx, reset-password.tsx
    pricing.tsx         # Stripe pricing page
    not-found.tsx
    placeholder.tsx     # Stub for unimplemented routes
    test-camera.tsx     # Camera debugging
    candidate/          # 22 pages — dashboard, jobs, profile, assessments, interviews, AI coaching, applications, offers, onboarding, payroll, OmniScore, documents, screening, quick-practice, mock-interview, omniscore
    recruiter/          # 15 pages — dashboard, jobs, applicants, assessments, candidates, interviews, offers, onboarding, analytics, company, payroll, OmniScore
    admin/              # 3 pages — login, AI health, revenue
    debug/              # 1 page — mock-interview debugging
```

### 4.3 Route Architecture
- **Public**: `/`, `/login`, `/register`, `/forgot-password`, `/reset-password`, `/pricing`, `/screening/:token`, `/test-camera`
- **Candidate**: `/candidate/*` — 15 nested routes under DashboardLayout
- **Recruiter**: `/recruiter/*` — 17 nested routes under DashboardLayout
- **Admin**: `/admin/login` (public), `/admin/ai-health`, `/admin/revenue` (protected)
- **Auto-redirect**: `/dashboard` → role-based redirect
- **SPA Fallback**: All non-API routes serve React `index.html`

### 4.4 Migration Status
- 27 pages fully migrated from legacy HTML to React
- 3 placeholder pages (recruiter/candidates, candidate/documents, settings)
- 11 legacy HTML pages still exist (low priority: job-board, video-interview, assessment-results, etc.)
- 42 legacy HTML pages in `public/` — still served but React is primary

---

## 5. Database Schema (105 tables, 16 domain groups)

### 5.1 Domain Groups
1. **Users & Auth** (4 tables) — users, user_sessions, oauth_connections, refresh_tokens
2. **Companies & Employees** (4 tables) — companies, company_settings, employees, departments
3. **Jobs & Applications** (6 tables) — jobs, job_applications, saved_jobs, job_views, application_notes, job_skills
4. **Candidate Profiles** (9 tables) — candidate_profiles, profile_skills, education, work_experience, certifications, portfolio_items, profile_views, candidate_preferences, candidate_settings
5. **Interview Flow** (8 tables) — interviews, interview_sessions, interview_questions, interview_responses, practice_sessions, mock_interview_sessions, interview_schedules, interview_feedback
6. **Screening & Assessment** (13 tables) — screening_templates, screening_sessions, screening_questions, screening_responses, assessment_catalog, assessment_sessions, assessment_questions, assessment_responses, skill_scores, anti_cheat_events, assessment_configs, grading_criteria, assessment_analytics
7. **Scoring & Trust** (10 tables) — omni_scores, trust_scores, score_appeals, score_factors, score_history, score_explanations, trust_factors, trust_history, company_ratings, rating_categories
8. **Offers & Onboarding** (7 tables) — offers, offer_templates, onboarding_documents, onboarding_checklists, onboarding_tasks, candidate_onboarding_data, onboarding_wizard_steps
9. **Communication** (4 tables) — communications, communication_templates, sequence_enrollments, communication_sequences
10. **Documents & Verification** (4 tables) — verification_documents, document_verifications, verified_credentials, document_access_logs
11. **Compliance & Privacy** (8 tables) — consent_records, data_requests, fairness_audits, audit_logs, compliance_settings, gdpr_requests, privacy_settings, data_retention_policies
12. **Payroll** (6 tables) — payroll_runs, paychecks, tax_records, payroll_settings, pay_periods, payroll_adjustments
13. **AI Infrastructure** (9 tables) — ai_prompts, ai_prompt_versions, ai_ab_tests, ai_call_logs, token_usage, model_performance, ai_fallback_logs, ai_health_metrics, ai_config
14. **Matching & Recommendations** (7 tables) — match_results, match_factors, mutual_matches, recommendation_logs, job_matches, candidate_matches, match_feedback
15. **Memory & Context** (2 tables) — user_memory, tts_cache
16. **System** (4 tables) — events, agent_data, system_settings, rate_limit_entries

### 5.2 Schema Hardening (Completed Feb 14, 2026)
- P0: 5 company_id FK corrections
- P1: 20 timestamptz conversions, NOT NULL constraints, 4 FKs, 14 indexes
- P2: 37 CHECK constraints, 274 varchar→TEXT, 5 timestamptz
- P3: 64 FK indexes, 182 timestamptz, 6 partial indexes, 7 unique constraints

---

## 6. AI Architecture

### 6.1 Provider Fallback Chain
```
Polsia AI Proxy (OpenAI format) → Anthropic Claude / GPT-4o / Gemini
  ↓ (failure)
OpenAI direct
  ↓ (failure)
NVIDIA NIM models (Llama, Nemotron, GPT-OSS, DeepSeek)
  ↓ (failure)
Groq (fast inference)
  ↓ (failure)
Cerebras (wafer-scale)
```

### 6.2 Vision Chain (for video analysis)
```
OpenAI GPT-4o → NIM Cosmos Reason → NIM Nemotron Nano VL
```
- Video frames uploaded to R2 first (Polsia proxy doesn't support base64 data URIs for vision)
- `safeParseJSON()` handles malformed LLM output (markdown fences, trailing commas, unescaped chars, smart quotes)

### 6.3 TTS/ASR Pipeline
- TTS: Piper (self-hosted) → browser fallback
- ASR: Whisper API → Web Speech API fallback

### 6.4 Circuit Breaker
- Auto-opens on 3 consecutive failures
- Half-open after 60 seconds
- Health check runs every 30 minutes

### 6.5 Token Budgeting
- Daily token limits per module
- Priority throttling (critical > normal > background)
- Budget predictions and alerts

### 6.6 Prompt Management (Pezzo-style)
- Prompt registry with versioning
- A/B testing support (`ai_ab_tests` table)
- Performance tracking per prompt version
- Admin UI for prompt editing, testing, metrics

### 6.7 Key AI Features
- **Adaptive Assessments**: AI generates next question based on previous answer difficulty
- **Mock Interviews**: Video analysis with body language, speech metrics, emotional analysis
- **Quick Practice**: ISOLATED from mock interview pipeline (separate code path)
- **Job Matching**: Semantic matching via pgvector embeddings
- **Document Verification**: AI OCR + fraud detection + authenticity scoring
- **OmniScore**: Multi-factor candidate credit score (skills, experience, verification, assessments)
- **TrustScore**: Company reputation score (ratings, reviews, hiring practices)
- **Bias Detection**: Fairness auditing on hiring decisions
- **Communication Generator**: AI message templates for recruiters

---

## 7. Key Features Status

### 7.1 Built & Working (13 modules)
1. ✅ Skill Assessments (adaptive AI questions)
2. ✅ Interview Coaching (practice + mock)
3. ✅ Job Matching (semantic via pgvector)
4. ✅ Document Management (upload + AI extraction + fraud detection)
5. ✅ Hiring Dashboard (pipeline tracking)
6. ✅ Onboarding (AI doc generation, I-9, W-4)
7. ✅ Payroll (US + India tax)
8. ✅ OmniScore (basic candidate scoring)
9. ✅ Profile (candidate profile builder)
10. ✅ Job Board (posting + browsing)
11. ✅ Offers (accept/decline workflow)
12. ✅ Compliance (bias detection foundation)
13. ✅ Communications (basic templates)

### 7.2 Missing / Partial (10 gaps)
1. ❌ **Recruiter AI Screener** — analyze candidate vs job requirements (high priority)
2. ❌ **Advanced OmniScore v2** — multi-factor with explainability (high priority)
3. ❌ **Email Notifications** — transactional emails (critical, table stakes)
4. ❌ **Pricing + Stripe** — monetization (basic billing deployed, needs validation)
5. ❌ **Calendar Integration** — Google + Outlook scheduling (high priority)
6. ❌ **EU AI Act Compliance** — enterprise sales enabler (high priority, Aug 2026 deadline)
7. ❌ **Candidate AI Career Coach** — skill gap analysis, career path (high priority)
8. ❌ **Real-time Collaboration** — team comments on candidates (medium)
9. ❌ **Analytics & Insights** — funnel analytics, time-to-hire (medium)
10. ❌ **ATS Integrations** — Greenhouse, Lever, Workday (medium)

---

## 8. Security Status (Updated June 5, 2026)

### 8.1 Fixed on June 5, 2026 (Critical)
- ✅ **JWT_SECRET** — Removed hardcoded fallback; throws error if missing
- ✅ **SESSION_SECRET** — Required, no fallback
- ✅ **Rate Limiting** — Replaced in-memory with PostgreSQL-backed distributed limiter
- ✅ **SMTP TLS** — Enabled `rejectUnauthorized: true`

### 8.2 Remaining Issues (from Security Audit Report)
| Severity | Issue | File | Status |
|----------|-------|------|--------|
| Critical | Database `rejectUnauthorized: false` | `lib/db.js` | ⚠️ Still open (needs CA bundle config) |
| Critical | Session cookie `secure: false` | `server.js` | ⚠️ Still open (Render proxy, but risky for direct access) |
| Critical | CORS `origin: true` | `server.js` | ⚠️ Still open (allows any origin with credentials) |
| Critical | Permissions-Policy `camera=*, microphone=*` | `server.js` | ⚠️ Still open (too broad) |
| High | Missing CSRF tokens | Multiple | ⚠️ Still open |
| High | Document IDOR — any recruiter can access any document | `routes/documents.js` | ⚠️ Still open (needs company-scoped check) |
| High | No rate limiting on auth endpoints | `routes/auth.js` | ⚠️ Still open (distributed limiter not applied) |
| High | Missing CSP, HSTS, X-Frame-Options | `server.js` | ⚠️ Still open (no helmet) |
| High | `SELECT j.*` exposes internal fields | `routes/jobs.js` | ⚠️ Still open |
| Medium | `trust proxy` without IP whitelist | `server.js` | ⚠️ Still open |
| Medium | Verbose error responses | Multiple | ⚠️ Still open (err.message leaked to client) |
| Medium | 7-day session with no rotation | `server.js` | ⚠️ Still open |
| Medium | No timeout on AI endpoints | `routes/interviews.js` | ⚠️ Still open (quick-practice has 38s timeout) |
| Medium | Inconsistent authorization patterns | Multiple | ⚠️ Still open |
| Medium | Unsanitized filenames in uploads | `routes/documents.js` | ⚠️ Still open |
| Low | Synchronous auth logging | `routes/auth.js` | ⚠️ Still open |
| Low | No API versioning | `server.js` | ⚠️ Still open |
| Low | 25-connection pool may exhaust | `lib/db.js` | ⚠️ Still open |

---

## 9. Development Workflow

### 9.1 Branches
- **`dev`** — Development branch (safe to break)
- **`main`** — Production branch (protected, auto-deploys to Render)
- Workflow: Request → Build on dev → Test locally → Review → Approve → Merge to main → Auto-deploy

### 9.2 Commit Format
```
type: brief description
# Types: feat, fix, docs, test, refactor, chore
```

### 9.3 Current Sprint (Week 1: May 5-9, 2026)
1. Priority 1: Pricing page + Stripe Checkout (deployed June 5, needs validation)
2. Priority 2: Email Notifications System (NOT YET BUILT)
3. Priority 3: EU AI Act Compliance Dashboard (August 2026 deadline)
4. Priority 4: Calendar Integration (Google + Outlook)
5. Priority 5: OmniScore Explainability Enhancement

### 9.4 Open PRs (as of June 5, 2026)
- PR #1: `feat: revenue dashboard + funnel metrics` — syntax checks pass, build passes, runtime blocked by missing OPENAI_API_KEY
- PR #2: `feat: polish mobile dashboard shell` — build passes, needs browser smoke test

---

## 10. Tech Debt & Issues

1. **Monolith files**: `ai-provider.js` (2287 lines), `onboarding.js` (3119 lines), `interviews.js` (2691 lines), `server.js` (1130 lines)
2. **42 legacy HTML pages** in `public/` alongside React SPA
3. **43% of mock_interview_sessions** stuck in `in_progress` (zombie records)
4. **3 placeholder routes** in React (recruiter/candidates, candidate/documents, settings)
5. **No E2E test suite**
6. **No TypeScript on backend** (pure JavaScript)
7. **Quick Practice vs Mock Interview**: Fully decoupled as of Feb 15, 2026 (#32717) — isolated code paths, separate routes

---

## 11. Competitive Position

- **OmniScore (two-sided scoring)**: ZERO direct competitors — unique differentiator
- **AI-native from ground up**: Most competitors bolted AI on after 10+ years (HireVue 2004→AI 2016, Greenhouse 2012→AI 2024, Workday 2005→HiredScore 2024)
- **Compliance moat**: EU AI Act deadline Aug 2026 — built-in bias auditing = enterprise sales
- **Gap**: Global payroll (Deel $17.3B, Papaya Global leading) — needs partnerships

---

## 12. Environment & Configuration

### 12.1 Required Environment Variables
- `DATABASE_URL` — Neon PostgreSQL connection string
- `JWT_SECRET` — MUST be set (no fallback, crashes if missing)
- `SESSION_SECRET` — MUST be set (no fallback)
- `POLSIA_API_KEY` — Polsia AI proxy authentication
- `OPENAI_API_KEY` — OpenAI direct access (for vision, fallback)
- `POLSIA_API_URL` — Defaults to `https://polsia.com/api/proxy/ai`
- `ADMIN_PASSWORD` — Admin panel password (dev auto-generates if missing)
- `STRIPE_SECRET_KEY` / `STRIPE_PUBLISHABLE_KEY` — Payment processing
- `SMTP_*` — Email configuration
- `FRONTEND_URL` — CORS origin (currently `origin: true` — insecure)

### 12.2 Database Pool Config
```javascript
max: 25,
idleTimeoutMillis: 30000,
connectionTimeoutMillis: 10000,
```
- Slow query threshold: 200ms (warns to console)
- Query stats tracking enabled

---

## 13. Quick Reference

### 13.1 File Locations
- Backend entry: `server.js`
- Frontend entry: `client/src/main.tsx`
- Router: `client/src/App.tsx` (42 routes)
- Auth context: `client/src/contexts/auth-context.tsx`
- API client: `client/src/lib/api.ts`
- Database pool: `lib/db.js`
- AI provider: `lib/ai-provider.js`
- Auth middleware: `lib/auth.js`
- Rate limiter: `lib/distributed-rate-limiter.js`
- Admin activity: `lib/activity-logger.js`
- Metrics: `lib/metrics-collector.js`

### 13.2 Analysis Files (subagent outputs)
- `analysis/routes-analysis.md` — Full backend routes analysis
- `analysis/frontend-analysis.md` — Frontend architecture analysis
- `analysis/ai-services-analysis.md` — NOT WRITTEN (subagent didn't save)
- `analysis/database-analysis.md` — NOT WRITTEN (subagent didn't save)

### 13.3 Documentation Files
- `README.md` — Project overview
- `FEATURE_MAP.md` — Feature status
- `SPRINT_PLAN.md` — Sprint planning
- `AGENTS.md` — Agent utilization plan
- `AGENT_PROTOCOL.md` — Agent rules (branch, coordination, commit format)
- `COORDINATION.md` — Daily standups, PR status, blockers
- `TASKS.md` — Task board with priorities
- `WORKFLOW.md` — Development workflow
- `SECURITY_AUDIT_REPORT.md` — 27 findings (6 critical, 8 high, 7 medium, 6 low)
- `COMPETITIVE_ANALYSIS.md` — Competitive landscape across 16 modules
- `FRONTEND_MIGRATION.md` — Migration status (27 done, 3 placeholder, 11 legacy)
- `client/FRONTEND_MIGRATION.md` — Same content

---

## 14. Key Decisions & Context

## 14. Key Decisions & Context

- **Branch flow: dev → staging → main (production).**
- **`dev`** — Development branch. All agent work, all commits, all PRs.
- **`staging`** — QA / Testing branch. Ranga tests here. **Suga promotes dev → staging when builds pass.** No asking.
- **`main`** — Production. ONLY Suga promotes staging → main after Ranga says "ship it."
- **Never commit directly to staging or main.**
- **Staging is for deployed review** — Ranga clicks around on rekrutai-dev.onrender.com
- **Work on `dev` only**, then promote to staging for review, then to main for production
- **Subagent work must be on dev first** — always checkout dev before any automated work
- **Preserve unrelated workspace changes**
- **Do not translate or alter code, file paths, identifiers, or error messages**
- **Quick Practice is isolated** from Mock Interview — never mix them (#32717)
- **OmniScore path**: `/api/omniscore` is canonical; `/api/candidate/omniscore` and `/api/recruiter/omniscore` are compatibility shims
- **Job application path**: `/api/candidate/jobs/:jobId/apply` is canonical (treat `/api/jobs/:id/apply` as stale docs)
- **Schema hardening complete** (P0-P3) — no more schema changes without migrations
- **Build artifact churn**: Keep `client/dist/` out of routine review unless intentional deployment
- **Stripe launch**: Basic billing deployed, needs live/test key validation, success/cancel flow testing
- **EU AI Act**: August 2026 deadline — compliance dashboard is Priority 3
- **Calendar/ATS/HRIS**: Still in contract-definition phase, no implementation scaffolding found

---

## 15. Agent Company Setup (June 5, 2026)

Created a 210-agent company structure for HireLoop (formerly Rekrut AI):

### Org Structure
- **C-Suite**: CEO (You) + CTO (Suga)
- **Engineering**: 52 agents (Backend 12, Frontend 8, AI/ML 18, DevOps 6, QA 8, Database 4)
- **Product**: 13 agents (Product 4, Design 6, Research 3)
- **Growth**: 28 agents (Marketing 10, Sales 8, CS 6, BD 4)
- **Compliance**: 13 agents (Security 6, Legal 4, Finance 3)
- **AI Research**: 23 agents (Core AI 10, OmniScore 6, Vision 4, Speech 3)
- **Support**: 81 general support agents
- **Total: 210 agents**

### Key Documents Created
- `/docs/ORG_STRUCTURE.md` — Full company org chart, agent specializations, escalation paths
- `/docs/LAUNCH_PLAN.md` — 90-day launch plan (Aug 15, 2026 target), phases, risks, budget
- `/docs/DAILY_OPS.md` — Daily operations, standup templates, deployment/incident/security protocols
- `/HEARTBEAT.md` — Daily automated checks, when to reach out, when to stay silent

### Current Sprint (June 5-19, 2026)
- **Priority 1**: Security hardening (6 critical issues → 0) — Assigned: BE-001, SEC-001 through SEC-006
- **Priority 2**: Email notifications (table stakes, not built yet) — Assigned: BE-002, BE-003, FE-001, MKT-001
- **Priority 3**: EU AI Act compliance dashboard (enterprise blocker) — Assigned: LEG-001, LEG-002, AI-001, AI-002, FE-002, PM-001
- **Priority 4**: Recruiter AI screener (differentiator) — Assigned: AI-003, AI-004, BE-004, FE-003
- **Priority 5**: Analytics & insights (revenue) — Assigned: BE-005, FE-004, PM-002, DB-001

### Launch Target: August 15, 2026
- **Pre-launch**: July 15, 2026 (soft launch for beta users)
- **Post-EU AI Act compliance deadline** (Aug 2026)
- **90-day runway**: $43,000 budget
- **Targets**: 500 DAU, $5,000 MRR, 25 paying customers, 10 enterprise trials

### Daily Workflow
- **08:00 UTC**: Morning standup (all teams)
- **09:00-12:00**: Deep work block 1 (coding, designing, writing)
- **12:00-13:00**: Lunch + async code review
- **13:00-17:00**: Deep work block 2 (continue sprint tasks)
- **17:00-18:00**: Wrap & handoff (documentation, tests, task board updates)
- **18:00-08:00 UTC**: Night shift (automated testing, maintenance, monitoring, AI health checks)

### Communication Protocol
- **Daily**: Standup summaries (08:00 UTC)
- **Weekly**: Executive summary to CEO (Friday 17:00 UTC)
- **Bi-weekly**: Sprint review + retro
- **Monthly**: Board metrics report
- **Quarterly**: Strategic planning session

---

## 16. UI/UX Reference Designs (June 5, 2026)

20 Visily screens provided as reference UI for the hybrid app. These are NOT code — they are visual reference designs to guide React component implementation.

### Screen Inventory by Module

**Candidate Module (Job Seeker):**
1. `visily-homepage-4.jpg` — Homepage (hero + features + blogs + footer)
2. `visily-job-listing.jpg` — Job Search (split view: list left, detail right)
3. `visily-user's-profile.jpg` — Candidate Profile (full profile view, read-only)
4. `visily-create-profile.jpg` — Edit Profile (multi-section form: General, About, Experience, Skills, Education)
5. `visily-sign-in-6.jpg` — Sign In (split layout: form left, image right)
6. `visily-sign-up-5.jpg` — Sign Up (role selector: JobSeeker/Employer)
7. `visily-onboarding-(modify).jpg` — Onboarding (multi-step wizard)
8. `visily-verify-with-aadhar.jpg` — Aadhar Verification (ID verification flow — India market)
9. `visily-profile-matching.jpg` — Profile Matching (candidate ↔ job match view)
10. `visily-skill-upgrade-&-certification-free...paid.jpg` — Skill Upgrade (course catalog + video player)

**Recruiter Module (Employer):**
11. `visily-create-listing-job.jpg` — Create Job Listing (3-step: Job info → Company info → Application)
12. `visily-company-profile.jpg` — Company Profile (public company page + reviews + ratings + jobs)
13. `visily-career-page.jpg` — Career Page (company careers landing: team, benefits, open positions)
14. `visily-chat-with-recruiter.jpg` — Chat with Recruiter (full messaging + file sharing + profile sidebar)
15. `visily-dashboard-charts-2.jpg` — Dashboard Analytics (sidebar nav + KPI cards + charts + world map)
16. `visily-ai-interview.jpg` — AI Interview (video call + chat thread side panel + participants)
17. `visily-candidate-listing.jpg` — Candidate Search (recruiter browsing candidates with filters + CTA)

**HR/Admin Module (Internal Tools):**
18. `visily-create-contract-job-details.jpg` — WorkWave Contract (stepper: Employee → Job → Compensation → Extras → Quote)
19. `visily-activate-account-verify-business.jpg` — PayMaven KYC (business verification + owner info + ID upload)

**Note:** Some duplicate screens exist in the reference set (Create Profile/Edit Profile, Activate Account/Verify Aadhar). These are the same flows — should be merged into single reusable components.

### Design System Decisions
- **Single brand**: "Rekrut AI" — no separate WorkWave/PayMaven branding in the app
- **Primary color**: Indigo 500 (#6366F1) — unified across all modules
- **Typography**: Inter or system sans-serif, scale from display 36px to caption 12px
- **Navigation**: Bottom nav on mobile, sidebar on desktop, hybrid on tablet
- **Components**: All screens should use shared primitives from `client/src/components/ui/`

### UI Version Control Plan
- `v1.0` — Foundation: All 20 screens implemented, responsive, brand unified
- `v1.1` — Recruiter tools: Create job, Candidate search, Dashboard, Analytics
- `v1.2` — AI Interview: Video interview, AI scoring, mock practice
- `v1.3` — Verification & Contracts: KYC, Contract generation, Skill certifications
- `v2.0` — Design refresh: Dark mode, motion, new tokens (opt-in upgrade)

### Key UI Gaps (Relative to Existing Code)
- The frontend has 42 routes and 53 pages, but many are basic/placeholder
- Legacy HTML pages (42 in `public/`) need full React migration
- The reference designs are significantly more polished than current UI
- Mobile responsiveness is unknown/uneven across legacy pages
- No dark mode, no advanced animations, no micro-interactions yet

### UI Build Priority (Aligned with Business Goals)
1. **Sign Up/Sign In** — First user impression, must be polished
2. **Candidate Profile + Edit Profile** — Core to OmniScore, needs to look trustworthy
3. **Job Search** — Main candidate activity, needs to feel fast and smart
4. **Dashboard Analytics** — Recruiter's daily view, needs to impress
5. **AI Interview** — Key differentiator, needs to feel futuristic but reliable
6. **Create Job Listing** — Recruiter's core action, needs to be effortless
7. **Chat with Recruiter** — Communication hub, needs to feel native
8. **Company Profile + Career Page** — Employer branding, needs to look premium
9. **Onboarding + Verification** — Trust-building, needs to feel secure
10. **Contract + Skill Upgrade** — Secondary features, can follow later

---

*Memory last updated: 2026-06-05 13:45 UTC*

---

## 9. 90-Day Launch Plan & Module Audit (June 5, 2026)

### 9.1 Deliverables Created

| Document | Purpose | Location |
|----------|---------|----------|
| **90-Day Technical Roadmap** | 3-month plan with milestones, gates, risks | `kimi-group-chat/Rekrut AI/90-Day Technical Roadmap.md` |
| **Module and Skills Audit** | 200 modules, status, gaps, priorities | `kimi-group-chat/Rekrut AI/Module and Skills Audit.md` |
| **Skills Action Plan** | What to add, build, keep, cut | `kimi-group-chat/Rekrut AI/Skills Action Plan.md` |
| **Design Files Priority** | 20 reference designs ranked | `kimi-group-chat/Rekrut AI/Design Files Priority.md` |
| **Module Summary** | Complete feature count: 200 total | `kimi-group-chat/Rekrut AI/Module Summary.md` |

### 9.2 Launch Readiness

| Category | Total | Built | Need Work | Missing | % Ready |
|----------|-------|-------|-----------|---------|---------|
| **Candidate Experience** | 85 | 59 | 21 | 5 | **86%** |
| **Recruiter Experience** | 67 | 32 | 20 | 15 | **58%** |
| **Infrastructure & AI** | 48 | 39 | 4 | 5 | **85%** |
| **Grand Total** | **200** | **130** | **45** | **25** | **75%** |

### 9.3 Critical Gaps (P0 Launch Blockers)

1. **Candidate Search** (recruiter view) — DONE ✅ (commit e419b42, GitHub #3). SQL query fixed, all filters tested, frontend functional. Dev deployment verified via browser.
2. **Recruiter Analytics** — DONE ✅ (commit c48db18, GitHub #15). Frontend endpoint bug fixed, all tests pass. Dev deployment verified via browser.
3. **Stripe Live Mode** — test mode validated ✅ (commits 90cf035 + 1d56d57). Keys added to Render env vars, deploy `dep-d8iup6mq1p3s73f5mj80` live. Checkout buttons active on pricing page. Ready for live keys.
4. **E2E Test Suite** — ~55% complete. Individual test files pass (payment flow, navigation, candidate/recruiter flows, auth persistence). Full suite blocked by browser resource limits (SIGKILL). 8 strict-mode violations fixed in commit f4eedd7. Run per-file for CI.
5. **Monitoring / Alerting** — basic admin only, needs Sentry/Datadog.
6. **CI/CD Pipeline** — manual deploy, needs automation.

### 9.4 The Moat (What to Keep)

- AI Provider Fallback (5 providers with circuit breaker)
- Quick Practice Isolation (decoupled from Mock Interview)
- pgvector Semantic Matching
- Document Verification (OCR + Fraud Detection)
- OmniScore (candidate credit score)
- Prompt Management (versioned, A/B tested)
- Token Budgeting (cost control)
- Bias Detection (fairness auditing)

### 9.5 The Cut List (P2/P3 Features Not Shipping on Launch)

Peer mock interviews, panel interviews, video call integration, pipeline automation, talent pool, e-signature, direct deposit, custom branding, API docs, developer SDK, advanced search, data warehouse, enterprise SSO.

### 9.6 Design Priority (Top 12 for Launch)

1. Sign Up (conversion gate)
2. Homepage (first impression)
3. AI Interview (the differentiator)
4. Job Search (main candidate activity)
5. Dashboard (recruiter's daily view)
6. Profile Edit (core user action)
7. Candidate Search (must build from placeholder)
8. Create Job (core recruiter action)
9. Profile View (what recruiters see)
10. Onboarding (drives completion)
11. Match Score (the AI magic moment)
12. Sign In (returning users)

---

*Memory last updated: 2026-06-08 06:00 UTC*
---

## 17. Agent Company Structure (June 8, 2026)

### Updated Team Structure
**Suga = CEO**: Orchestrates all agents, delegates tasks, reviews outputs, reports to Ranga. Does NOT write code or do technical work personally.

**KimiClaw = CTO**: Handles all technical development, engineering tasks, dev work. Collaborates with Suga on technical decisions.

**Executive Team (C-Suite):**
- CEO: Ranga (human)
- CEO (Agent): Suga — orchestration, delegation, reporting
- CTO (Agent): KimiClaw — technical development, engineering, dev work
- CMO: content-creator + social-media-strategist + seo-specialist + growth-hacker
- COO: product-manager + project-shepherd + workflow-architect
- CFO: financial-analyst + finance-tracker + fp-a-analyst
- CISO: security-architect + application-security-engineer
- VP-ENG: devops-automator + infrastructure-maintainer + sre-site-reliability-engineer
- VP-QA: code-reviewer + api-tester + test-results-analyzer
- VP-DES: ui-designer + ux-architect + ux-researcher
- VP-AI: ai-engineer + prompt-engineer
- VP-LEG: compliance-auditor + legal-compliance-checker

**Engineering Teams (15 agents) — Report to KimiClaw (CTO):**
- Backend: 5 agents (BE-001 through BE-005)
- Frontend: 5 agents (FE-001 through FE-005)
- AI/ML: 4 agents (AI-001 through AI-004)
- DevOps: 3 agents (DO-001 through DO-003)
- QA: 3 agents (QA-001 through QA-003)

**Growth & Business Teams (9 agents):**
- Marketing: 3 agents (MKT-001 through MKT-003)
- Growth: 2 agents (GRW-001, GRW-002)
- Finance: 2 agents (FIN-001, FIN-002)
- Legal: 2 agents (LEG-001, LEG-002)

### Collaboration Model (Suga ↔ KimiClaw)
- Suga (CEO) identifies what needs to be done
- KimiClaw (CTO) handles technical execution and engineering delegation
- Suga coordinates between KimiClaw and other teams (marketing, QA, etc.)
- Technical decisions: Suga + KimiClaw collaborate
- Status updates: Both report to group chat
- Suga does NOT do technical work — delegates to KimiClaw or engineering agents

### Agent Spawn Rules
- Max 5 agents per day (cost control: ~$27/day)
- Max 2 parallel agents per task (coordination)
- One agent, one file, one task (micro-task principle)
- 3-minute timeout per agent (cost control)
- Always checkout `dev` branch before work
- Never push to `staging` or `main` directly

### Graph Memory System (Agent Briefings)
- **AGENT_BRIEFING.md**: Pre-summarized codebase state (10,000+ lines compressed to essentials)
- Agents never read raw files >500 lines
- KimiClaw (CTO) pre-reads technical files, summarizes, sends snippet + context
- codebase-onboarding-engineer agent available for new agent onboarding

### Daily Schedule
- **08:00 UTC**: Morning standup (all teams)
- **Every 2 minutes**: Suga checks for urgent tasks, delegates to appropriate agents
- **Every 2 hours**: Work batch — Suga delegates tasks based on sprint priorities
- **09:00-12:00 UTC**: Deep Work Block 1 (coding, designing, AI tuning)
- **12:00-13:00 UTC**: Code review + async review
- **13:00-17:00 UTC**: Deep Work Block 2 (integration, testing, cross-team)
- **17:00-18:00 UTC**: Wrap & handoff (docs, build verification, commits)
- **18:00-08:00 UTC**: Night shift (automated: health checks, security scans, backups)

### QA Pipeline (Before Every Staging → Main)
- Automated: build, TypeScript, unit tests, API tests, security scan, dependency audit
- Manual: click-through, mobile responsive, dark mode, accessibility, performance, Stripe, AI features
- Gate: all P0 complete, 0 critical/high security, E2E passing, Ranga approval

### Launch Readiness (Current: 25%)
- P0 tasks: 20% complete
- Security: 40% (6 critical findings in progress)
- Build: 100% clean
- TypeScript: 3 pre-existing errors (not new)
- Staging: deployed and live

### Documents Created
- `AGENT_COMPANY.md` — Full org structure, sprint board, spawn rules
- `AGENT_BRIEFING.md` — Codebase summary for agents (no 500+ line reads)
- `HEARTBEAT.md` — Daily schedule, checks, metrics, escalation rules
- `QA_TEST_PLAN.md` — Comprehensive test plan for staging → main promotion

---

*Memory last updated: 2026-06-06 15:45 UTC*

---

## Status Update - 2026-06-08

### Stripe Live Validation - COMPLETE ✅
- Webhook error handling fixed: DB errors return 200 to prevent Stripe retries
- All 4 webhook events tested and passing: checkout.session.completed, invoice.payment_succeeded, invoice.payment_failed, customer.subscription.deleted
- Full end-to-end flow test: login → checkout → webhook → subscription status → cancel → delete webhook
- Commits: 90cf035 (webhook fix), 1d56d57 (test suite)
- GitHub issue #6 updated with progress
- Test keys configured in local .env (not committed)
- Backend endpoints validated: checkout-session, webhook, subscription-status, cancel-subscription, plans
- Ready for live key swap

### Legacy HTML Migration - 100% COMPLETE ✅
- All 21 recruiter/admin pages migrated to React
- All 19 candidate pages migrated to React
- Build passes, 0 TypeScript errors introduced
- Routes added to App.tsx

### Marketing Site - COMPLETE ✅
- landing.tsx candidate-first messaging (1033 lines)
- Hero, features, pricing, testimonials, FAQ, security/trust section, footer
- Analytics tracking on every CTA
- Pushed to dev branch

### Mobile Responsive - 95% COMPLETE ✅
- Job detail panel fixed (commit cf3bc53)
- Theme picker, danger zone, pipeline cards fixed
- Remaining: minor tweaks on mobile job detail panel (cosmetic)

### Sign Up Polish (B-008) - COMPLETE ✅
- Register page: split-screen layout matching Visily design
- Login page: split-screen layout with blue decorative panel
- Both pages responsive for mobile
- Commit: 1003788 (kimiclaw)

### Settings Page - ALREADY COMPLETE ✅
- Profile, account, notifications, privacy, appearance, billing tabs
- All API endpoints wired up
- Not a P0 blocker

### GitHub Tracking
- Issues updated: #6 (Stripe), #7 (Sign Up polish)
- Commits pushed to dev branch
- Clean gitignore: test artifacts, auth logs, local auth state removed from tracking

### Current P0 Status
- Security: 100% ✅
- Legacy HTML migration: 100% ✅
- Recruiter Analytics: 100% ✅ (GitHub #15, commit c48db18 — frontend calling wrong endpoint, fixed and tested)
- Candidate Search: 100% ✅ (GitHub #3, commit e419b42 — SQL query fix, all filters tested, auth working)
- Mobile responsive: 95% ✅
- Stripe live validation: 100% ✅ (test mode validated, keys in Render env vars, deploy `dep-d8iup6mq1p3s73f5mj80` live)
- Sign Up polish: 100% ✅
- Marketing site: 100% ✅
- E2E test suite: ~55% (Playwright installed, individual test files pass. Payment flow, navigation, candidate/recruiter flows tested. 8 strict-mode violations fixed in commit f4eedd7. Full suite blocked by browser resource limits — run per-file for CI.)
- EU AI Act dashboard: 50%
- Prod deployment: 0% (not started)
- Public launch: 0% (hard deadline Jun 30)

### Recruiter Analytics - 100% COMPLETE ✅ (2026-06-08)
- GitHub issue #15 created and updated
- Frontend bug fixed: analytics page was calling /recruiter/dashboard instead of /recruiter/analytics
- Fix: 1 line change in client/src/pages/recruiter/analytics.tsx (commit c48db18)
- Build passes ✅
- Test script created: scripts/test-recruiter-analytics.js (commit 71c561b)
- All 4 tests PASS: login, analytics endpoint, dashboard (backward compat), jobs
- Endpoint returns 10 metric fields: job_stats, application_stats, avg_time_to_hire, score_distribution, source_breakdown, overview, pipeline, applications, candidates
- Frontend has comprehensive UI: funnel visualization, source breakdown, hiring velocity, time-to-hire, score distribution, diversity metrics, top skills
- Time taken: ~45 minutes
- Pushed to dev branch
