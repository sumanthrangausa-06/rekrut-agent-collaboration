# Rekrut AI — Production Deployment Readiness Checklist

> **Prepared by:** DevOps Automator (DO-001)  
> **Date:** 2026-06-09 03:55 GMT+8  
> **Current Branch:** `dev` (local workspace)  
> **Production Service:** `rekrutai-prod` (Render)  
> **Staging Service:** `rekrutai-dev` (Render)  
> **Production URL:** `https://rekrutai.co`  
> **Staging URL:** `https://rekrutai-dev.onrender.com`  
> **Overall Status:** ⚠️ PARTIALLY READY — 3 blockers + 5 warnings remaining

---

## Table of Contents
1. [Live Environment Health Checks](#1-live-environment-health-checks)
2. [Render Deployment Configuration](#2-render-deployment-configuration)
3. [Environment Variables Audit](#3-environment-variables-audit)
4. [Database & Migration Status](#4-database--migration-status)
5. [Domain / DNS & SSL](#5-domain--dns--ssl)
6. [Build Artifacts & Code Readiness](#6-build-artifacts--code-readiness)
7. [Git & Branch Status](#7-git--branch-status)
8. [Critical Blockers](#8-critical-blockers)
9. [Warnings & Risks](#9-warnings--risks)
10. [Deployment Execution Plan](#10-deployment-execution-plan)
11. [Post-Deploy Smoke Tests](#11-post-deploy-smoke-tests)
12. [Rollback Plan](#12-rollback-plan)
13. [Appendix: Useful Commands](#13-appendix-useful-commands)

---

## 1. Live Environment Health Checks

### 1.1 Production — `https://rekrutai.co`

Verified: **2026-06-09 03:55 GMT+8**

| Check | Endpoint | Result | Status |
|-------|----------|--------|--------|
| **Root Health** | `GET /health` | HTTP 200, `{"status":"ok"}` | ✅ PASS |
| **API Health** | `GET /api/health` | HTTP 200, `{"status":"ok"}` | ✅ PASS |
| **Homepage** | `GET /` | HTTP 200, renders correctly | ✅ PASS |
| **Jobs API** | `GET /api/jobs?limit=1` | HTTP 200, returns job data | ✅ PASS |
| **Response Time** | `/health` | ~1.0s (warm) | ✅ PASS |

### 1.2 Production Security Headers

| Header | Expected | Actual | Status |
|--------|----------|--------|--------|
| `x-powered-by` | ABSENT | ABSENT | ✅ PASS |
| `content-security-policy` | Present (helmet) | Present (full CSP) | ✅ PASS |
| `strict-transport-security` | `max-age=31536000` | `max-age=31536000; includeSubDomains; preload` | ✅ PASS |
| `x-frame-options` | `SAMEORIGIN` | `SAMEORIGIN` | ✅ PASS |
| `x-content-type-options` | `nosniff` | `nosniff` | ✅ PASS |
| `permissions-policy` | `camera=(self), microphone=(self)` | `camera=(self), microphone=(self)` | ✅ PASS |
| `referrer-policy` | `no-referrer` | `no-referrer` | ✅ PASS |
| `cross-origin-opener-policy` | `same-origin` | `same-origin` | ✅ PASS |

> **Note:** Production is running newer code than the May 16 baseline (`fb1fdb3`). Security headers and `/api/health` are both functional, indicating the deployment has been updated since the last checklist.

### 1.3 Staging — `https://rekrutai-dev.onrender.com`

| Check | Endpoint | Result | Status |
|-------|----------|--------|--------|
| **Root Health** | `GET /health` | HTTP 200, `{"status":"ok"}` | ✅ PASS |
| **API Health** | `GET /api/health` | HTTP 200, `{"status":"ok"}` | ✅ PASS |
| **Homepage** | `GET /` | HTTP 200 | ✅ PASS |
| **Jobs API** | `GET /api/jobs?limit=1` | HTTP 200, returns job data | ✅ PASS |
| **Response Time** | `/health` | ~7.8s (cold start on starter plan) | ⚠️ SLOW |

### 1.4 Staging Security Headers

All 8 security headers verified present and correct — same as production.

---

## 2. Render Deployment Configuration

### 2.1 Services Defined in `render.yaml`

| Service | Type | Branch | Plan | `autoDeploy` | `numInstances` | Status |
|---------|------|--------|------|------------|----------------|--------|
| `rekrutai-prod` | web | `main` | standard | `false` | 1 | ✅ Configured |
| `rekrutai-staging` | web | `staging` | starter | `true` | 1 | ✅ Configured |
| `rekrutai-dev` | web | `dev` | starter | `true` | 1 | ✅ Configured |
| `rekrutai-prod-db` | pserv | `main` | standard | — | — | ✅ Configured |
| `rekrutai-staging-db` | pserv | `staging` | starter | — | — | ✅ Configured |
| `rekrutai-dev-db` | pserv | `dev` | starter | — | — | ✅ Configured |

### 2.2 Build & Start Commands

| Command | Configured Value | Status | Notes |
|---------|-----------------|--------|-------|
| `buildCommand` | `cd client && npm install --include=dev && npm run build && cd .. && npm install` | ✅ OK | Includes client build |
| `startCommand` | `npm run migrate && npm start` | ✅ OK | **Migration runs on boot** — previously a blocker, now fixed |
| `healthCheckPath` | `/health` | ✅ OK | |

### 2.3 Auto-Set Environment Variables (from `render.yaml`)

| Variable | Production Value | Status |
|----------|-----------------|--------|
| `NODE_ENV` | `production` | ✅ OK |
| `PORT` | `10000` | ✅ OK |
| `DATABASE_URL` | from `rekrutai-prod-db` | ✅ OK |
| `REKRUT_AI_URL` | `https://rekrutai.co` | ✅ OK |
| `APP_URL` | `https://rekrutai.co` | ✅ OK |
| `FRONTEND_URL` | `https://rekrutai.co` | ✅ OK |
| `BASE_URL` | `https://rekrutai.co` | ✅ OK |
| `CORS_ORIGINS` | `https://rekrutai.co,https://www.rekrutai.co` | ✅ OK |
| `FORCE_SSL_VERIFY` | `true` | ✅ OK |

---

## 3. Environment Variables Audit

### 3.1 Manual / Secret Variables (`sync: false` — MUST verify in Render Dashboard)

> ⚠️ **Cannot verify from local workspace. Must log in to Render Dashboard → `rekrutai-prod` → Environment tab.**

#### Tier 1 — Authentication (Deployment fails without these)

| Variable | Status | Required | Impact if Missing |
|----------|--------|----------|-------------------|
| `JWT_SECRET` | ⚠️ **VERIFY** | Critical | Auth completely fails |
| `SESSION_SECRET` | ⚠️ **VERIFY** | Critical | Sessions broken, login fails |
| `ADMIN_USERNAME` | ⚠️ **VERIFY** | Critical | Admin panel lockout |
| `ADMIN_PASSWORD` | ⚠️ **VERIFY** | Critical | Admin panel lockout |

#### Tier 2 — Revenue / Stripe (BLOCKER — no live payments without these)

| Variable | Status | Required | Impact |
|----------|--------|----------|--------|
| `STRIPE_SECRET_KEY` | 🔴 **BLOCKER — needs `sk_live_*`** | Critical | **Zero revenue** |
| `STRIPE_PUBLISHABLE_KEY` | 🔴 **BLOCKER — needs `pk_live_*`** | Critical | **Zero revenue** |
| `STRIPE_WEBHOOK_SECRET` | 🔴 **BLOCKER — needs live webhook secret** | Critical | Webhook validation fails |

**External Stripe action required:**
- [ ] Create live webhook endpoint in Stripe Dashboard: `https://rekrutai.co/api/billing/webhook`
- [ ] Copy `whsec_...` from live endpoint into Render Dashboard
- [ ] Ensure webhook events include: `checkout.session.completed`, `invoice.payment_succeeded`, `customer.subscription.created`, `customer.subscription.updated`, `customer.subscription.deleted`

#### Tier 3 — AI / Core Features

| Variable | Status | Impact |
|----------|--------|--------|
| `POLSIA_API_KEY` | ⚠️ **VERIFY** | AI features fail |
| `POLSIA_API_URL` | ⚠️ **VERIFY** | AI features fail |
| `OPENAI_API_KEY` | ⚠️ **VERIFY** | AI fallback fails |
| `OPENAI_BASE_URL` | ⚠️ **VERIFY** | AI fallback fails |
| `NVIDIA_NIM_API_KEY` | ⚠️ **VERIFY** | AI fallback fails |
| `NIM_BASE_URL` | ⚠️ **VERIFY** | NIM calls fail |
| `GROQ_API_KEY` | ⚠️ **VERIFY** | AI fallback fails |
| `CEREBRAS_API_KEY` | ⚠️ **VERIFY** | AI fallback fails |
| `DEEPGRAM_API_KEY` | ⚠️ **VERIFY** | TTS/STT fails |

#### Tier 4 — NIM Model-Specific Variables (~20 vars)

All `NIM_*` model-specific variables are `sync: false` in `render.yaml`. Verify each is populated if specific models are required. If empty, app falls back to defaults.

#### Tier 5 — Email / SMTP

| Variable | Status | Impact |
|----------|--------|--------|
| `EMAIL_HOST` / `PORT` / `USER` / `PASS` | ⚠️ **VERIFY** | Email notifications fail |
| `EMAIL_FROM_ADDRESS` / `EMAIL_FROM_NAME` | ⚠️ **VERIFY** | Email notifications fail |
| `SMTP_HOST` / `PORT` / `USER` / `PASS` / `FROM` | ⚠️ **VERIFY** | Email notifications fail |

#### Tier 6 — OAuth (Social login)

| Variable | Status | Impact | External Action Required |
|----------|--------|--------|--------------------------|
| `GOOGLE_CLIENT_ID` / `GOOGLE_CLIENT_SECRET` | ⚠️ **VERIFY** | Google OAuth fails | Add `https://rekrutai.co` to Google Cloud Console redirect URIs |
| `LINKEDIN_CLIENT_ID` / `LINKEDIN_CLIENT_SECRET` | ⚠️ **VERIFY** | LinkedIn OAuth fails | Add `https://rekrutai.co` to LinkedIn Developer Portal redirect URIs |
| `GOOGLE_REDIRECT_URI` | ⚠️ **VERIFY** | Google OAuth fails | Must be `https://rekrutai.co/api/auth/google/callback` |
| `LINKEDIN_REDIRECT_URI` | ⚠️ **VERIFY** | LinkedIn OAuth fails | Must be `https://rekrutai.co/api/auth/linkedin/callback` |

#### Tier 7 — Cloudflare R2 (Document storage)

| Variable | Status | Impact |
|----------|--------|--------|
| `R2_ACCESS_KEY_ID` / `R2_SECRET_ACCESS_KEY` | ⚠️ **VERIFY** | Document storage fails |
| `R2_BUCKET_NAME` / `R2_ENDPOINT` / `R2_PUBLIC_URL` | ⚠️ **VERIFY** | Document storage fails |

---

## 4. Database & Migration Status

### 4.1 Migration Inventory

| Type | Count | Files | Status |
|------|-------|-------|--------|
| JavaScript migrations | 54 | `001_*.js` → `051_*.js` | ✅ Present |
| SQL migrations | 2 | `045_fix_company_id_fk_constraints.sql`, `p2_schema_hardening.sql` | ⚠️ Not tracked by runner |
| Seed scripts | 1 | `seed_notification_templates.js` | ⚠️ Not tracked by runner |
| **Total** | **57** | | |

### 4.2 Critical Migration Issues

| Issue | Severity | Details | Status |
|-------|----------|---------|--------|
| **Duplicate prefixes** | 🔴 **Critical** | `003` (×2), `005` (×2), `035` (×2), `040` (×2) | ❌ **NOT FIXED** |
| **Non-numeric prefixes** | 🟡 **High** | `p2_schema_hardening.sql`, `p3_schema_optimizations.js`, `1739617200000_p1_interview_flow_schema.js` | ⚠️ Verify ordering |
| **SQL files not auto-applied** | 🟡 **High** | Custom `migrate.js` only tracks `.js` files. SQL files must be applied manually or via wrapper. | ⚠️ Verify wrappers applied |
| **Migration automation** | | `startCommand` now includes `npm run migrate && npm start` | ✅ **FIXED** |

> **Action Required:** Rename duplicate migration files before production deploy to avoid non-deterministic ordering:
> - `003_add_role_column.js` → `003b_add_role_column.js` (or renumber)
> - `005_oauth_refresh_tokens.js` → `005b_oauth_refresh_tokens.js`
> - `035_pg_sessions.js` → `035b_pg_sessions.js`
> - `040_mock_per_question_analysis.js` → `040b_mock_per_question_analysis.js`

### 4.3 Database Connectivity

| Environment | Database | Status | Response |
|-------------|----------|--------|----------|
| **Dev/Staging** | Neon PostgreSQL (`ep-calm-field-aipg6g97-pooler`) | ✅ Connected | `/api/jobs` returns data |
| **Production** | Render PostgreSQL (`rekrutai-prod-db`) | ✅ Connected | `/api/jobs` returns data |

### 4.4 Pre-Deploy Database Steps

| # | Step | Status | Notes |
|---|------|--------|-------|
| 4.4.1 | **Take production DB snapshot** | 🔴 **NOT DONE** | BLOCKER — must do before deploy |
| 4.4.2 | Rename duplicate migration files | 🔴 **NOT DONE** | Required before deploy |
| 4.4.3 | Verify `pgvector` extension on prod | ⚠️ **NOT VERIFIED** | `CREATE EXTENSION IF NOT EXISTS vector;` |
| 4.4.4 | Dry-run migrations on production | ⚠️ **NOT DONE** | Run `node migrate.js` with prod DB read-only if possible |

---

## 5. Domain / DNS & SSL

### 5.1 Domain Configuration

| Domain | Status | DNS / Redirect | Notes |
|--------|--------|--------------|-------|
| `rekrutai.co` | ✅ **ACTIVE** | Cloudflare → Render | Production endpoint. Returns 200. |
| `www.rekrutai.co` | ✅ **ACTIVE** | 301 → `rekrutai.co` | Redirect verified (HTTP 301) |
| `rekrutai-dev.onrender.com` | ✅ **ACTIVE** | Render native | Dev endpoint. Returns 200. |
| `rekrutai-staging.onrender.com` | ✅ **ACTIVE** | Render native | Staging endpoint. Returns 200. |

### 5.2 SSL Certificate Status

| Check | Status | Details |
|-------|--------|---------|
| HTTPS accessible on prod | ✅ Yes | `https://rekrutai.co` returns 200 |
| TLS certificate issuer | ✅ Google Trust Services (WE1) | Cloudflare managed |
| Certificate valid from | ✅ Apr 18, 2026 | |
| Certificate expires | ⚠️ **Jul 17, 2026** (~38 days) | Auto-renew expected via Cloudflare |
| HSTS header | ✅ Present | `max-age=31536000; includeSubDomains; preload` |
| `CORS_ORIGINS` includes `www` | ✅ Yes | Already in `render.yaml` |

> ⚠️ **SSL Expiry Warning:** Certificate expires July 17, 2026. Cloudflare auto-renewal is expected, but monitor 14 days before expiry.

### 5.3 DNS Pre-Deploy Checks

- [x] `rekrutai.co` A/CNAME records point to Render
- [x] `www.rekrutai.co` 301 redirect to apex verified
- [ ] Verify HTTPS certificate renewal is scheduled (Cloudflare auto-renew)
- [x] `CORS_ORIGINS` in `render.yaml` includes `www.rekrutai.co`

---

## 6. Build Artifacts & Code Readiness

### 6.1 Client Build Verification

| Check | Status | Details |
|-------|--------|---------|
| `client/dist/` exists | ✅ Yes | Present in repo |
| `client/dist/index.html` | ✅ Yes | Last built: **2026-06-09 03:54 GMT+8** |
| `client/dist/assets/index-*.js` | ✅ Yes | 1.56 MB main bundle |
| `client/dist/assets/index-*.css` | ✅ Yes | 102 KB CSS bundle |
| `client/dist/assets/vendor-*.js` | ✅ Yes | 49 KB vendor bundle |
| `client/dist/favicon.svg` | ✅ Yes | Present |
| Build freshness | ✅ Recent | Built ~1 hour ago |

### 6.2 Server & Route Syntax

| Check | Status | Details |
|-------|--------|---------|
| `server.js` syntax | ✅ Valid | `node -c server.js` passes |
| Route files syntax | ✅ Valid | All 23 route files pass syntax check |
| `package.json` test script | ✅ Present | `"test": "npx playwright test"` |
| `package.json` build script | ✅ Present | `"build": "cd client && npm install --include=dev && npm run build"` |
| `package.json` migrate script | ✅ Present | `"migrate": "node migrate.js"` |

### 6.3 .env & Security

| Check | Status | Details |
|-------|--------|---------|
| `.env` file exists | ✅ Yes | Development environment config |
| `.env` in `.gitignore` | ✅ Yes | Protected from accidental commit |
| `.env.example` exists | ✅ Yes | Template for new environments |

---

## 7. Git & Branch Status

### 7.1 Branch State (Local Workspace)

| Branch | Latest Commit | Relative to Remote | Status |
|--------|--------------|-------------------|--------|
| `dev` | `18af88f` | `origin/dev: behind` | WIP commits — E2E test updates, build artifacts |
| `main` | `c3d46f0` | `origin/main: ahead 2` | ⚠️ **2 local commits NOT pushed** |
| `staging` | `971e388` | `origin/staging: ahead 1` | ⚠️ **1 local commit NOT pushed** |
| `origin/main` | `c42fcc8` | — | Remote main branch |

### 7.2 Uncommitted Changes

| File | Status | Action Required |
|------|--------|-----------------|
| `debug-admin.js` | Untracked | Remove or commit to feature branch |
| `debug-staging.js` | Untracked | Remove or commit to feature branch |
| `qa-staging-report.md` | Untracked | Remove or commit to feature branch |

### 7.3 Commit Delta Since Last Known Production Deploy

| Metric | Value |
|--------|-------|
| Last known production baseline | `fb1fdb3` (May 16, 2026) |
| Commits since baseline | **172 commits** |
| Key changes | Security headers, mobile responsive, Stripe, E2E tests, React migration, Helmet, EU AI Act compliance |

> **Action Required:** Before production deploy, ensure `main` branch is clean and pushed to `origin/main`. The 2 local commits on `main` (EU AI Act dashboard + pre-deploy readiness) must be pushed if they are intended for production.

---

## 8. Critical Blockers

### 🔴 P0 — Must Fix Before Production Deploy

| # | Blocker | Owner | Impact | Resolution |
|---|---------|-------|--------|------------|
| **B1** | **Duplicate migration prefixes** | DO-001 | Non-deterministic migration order can corrupt database schema | Rename `003`, `005`, `035`, `040` duplicates (30 min) |
| **B2** | **Production DB snapshot not taken** | DO-001 | No safe rollback if data corruption on deploy | Take snapshot in Render Dashboard (15 min) |
| **B3** | **Stripe live keys not configured** | Ranga | **ZERO REVENUE** — no real payments possible | Set `sk_live_*`, `pk_live_*`, `whsec_*` in Render Dashboard + create live webhook endpoint (1–2 days) |

### 🟡 P1 — High Risk (Fix Within 1 Week Before Deploy)

| # | Risk | Owner | Impact |
|---|------|-------|--------|
| **R1** | `numInstances: 1` — no zero-downtime deploy | DO-001 | Every deploy causes ~30–60s downtime |
| **R2** | Local `main` 2 commits ahead of `origin/main` | DO-001 | If not pushed, production deploys old code. If pushed without review, risk of untested commits. |
| **R3** | Untracked debug/QA files in workspace | DO-001 | Clutter; risk of accidental commit |
| **R4** | E2E tests not confirmed on latest `main` | QA | Risk of broken critical flows in production |
| **R5** | 50+ production secrets (`sync: false`) not verified | Ranga + DO-001 | Auth, AI, OAuth, email may all fail on deploy |

### 🟢 P2 — Medium/Low (Fix After Launch)

| # | Risk | Owner | Impact |
|---|------|-------|--------|
| **R6** | No `npm audit` in build pipeline | DO-001 | Vulnerable dependencies can reach production |
| **R7** | No error tracking (Sentry/LogRocket) | DO-001 | Production bugs discovered by users |
| **R8** | No load testing (k6/Artillery) | DO-001 | Cannot verify SLA under expected traffic |
| **R9** | SSL certificate expires Jul 17 (~38 days) | DO-001 | Monitor renewal; Cloudflare auto-renew expected |
| **R10** | Staging DB on `starter` plan | DO-001 | Staging may not mirror prod performance |

---

## 9. Warnings & Risks

### 9.1 Migration Warnings

- **SQL files not tracked by `migrate.js`:** `045_fix_company_id_fk_constraints.sql` and `p2_schema_hardening.sql` are not automatically applied. The `p2_schema_hardening.js` wrapper exists and applies the SQL content, but verify `045_fix_company_id_fk_constraints.sql` has been applied manually on production.
- **Non-numeric prefix files:** `p3_schema_optimizations.js` and `1739617200000_p1_interview_flow_schema.js` sort differently than numeric files. Verify the `_migrations` table order on production to ensure they applied in the correct sequence.
- **Seed script:** `seed_notification_templates.js` must be run manually post-deploy if not triggered by startup logic.

### 9.2 Service Plan Warnings

- **Production:** `standard` plan, 1 instance — supports custom domains but no rolling deploys. Consider upgrading to `pro` or `numInstances: 2` post-launch if traffic spikes.
- **Staging:** `starter` plan — sufficient for QA but cold-start ~30s.
- **Dev:** `starter` plan — sufficient for development.

---

## 10. Deployment Execution Plan

### 10.1 Pre-Deploy (Complete ALL Before Deploy Day)

| # | Step | Owner | Time | Status |
|---|------|-------|------|--------|
| 10.1.1 | Rename duplicate migration files (003, 005, 035, 040) | DO-001 | 30 min | 🔴 **TODO** |
| 10.1.2 | Commit or remove untracked debug/QA files | DO-001 | 15 min | 🔴 **TODO** |
| 10.1.3 | Push local `main` commits to `origin/main` (if intended for prod) | DO-001 | 1 min | ⚠️ **TODO** |
| 10.1.4 | Tag release candidate | DO-001 | 5 min | 🔴 **TODO** |
| 10.1.5 | Take production DB snapshot | DO-001 | 15 min | 🔴 **BLOCKER** |
| 10.1.6 | Set all `sync: false` env vars in Render Dashboard | DO-001 + Ranga | 2–4 hrs | 🔴 **TODO** |
| 10.1.7 | **Stripe live keys** in Render Dashboard | Ranga | 30 min | 🔴 **BLOCKER** |
| 10.1.8 | Create live Stripe webhook endpoint | Ranga | 15 min | 🔴 **BLOCKER** |
| 10.1.9 | Update OAuth redirect URIs (Google, LinkedIn) | Ranga | 30 min | 🔴 **TODO** |
| 10.1.10 | Run full E2E suite against latest `main` | QA | 2–4 hrs | 🔴 **TODO** |
| 10.1.11 | Ranga Go/No-Go approval | Ranga | 30 min | 🔴 **TODO** |

### 10.2 Deploy Day (Execute in Sequence)

| # | Step | Action | ETA | Owner |
|---|------|--------|-----|-------|
| 10.2.1 | Verify `main` is clean | `git status` — no uncommitted changes | 1 min | DO-001 |
| 10.2.2 | Trigger manual deploy in Render Dashboard | `rekrutai-prod` → Manual Deploy → Latest Commit | 1 min | DO-001 |
| 10.2.3 | Monitor build logs | Render Dashboard → Logs | 3–5 min | DO-001 |
| 10.2.4 | Wait for health check | `curl -s https://rekrutai.co/health` | 1–2 min | DO-001 |
| 10.2.5 | Verify `/api/health` | `curl -s https://rekrutai.co/api/health` | 1 min | DO-001 |
| 10.2.6 | Verify security headers | `curl -I https://rekrutai.co/` — `x-powered-by` absent | 1 min | DO-001 |
| 10.2.7 | Run post-deploy smoke tests | Section 11 smoke tests | 15 min | QA + DO-001 |
| 10.2.8 | Seed notification templates (if needed) | Render Shell → `node migrations/seed_notification_templates.js` | 1 min | DO-001 |
| 10.2.9 | Monitor error logs | Render Dashboard → Logs | Ongoing | DO-001 |

### 10.3 Build Timeline Estimate

| Phase | Duration | Notes |
|-------|----------|-------|
| Manual trigger → build start | ~30s | `autoDeploy: false` requires manual trigger |
| Build phase | 3–5 min | Client build + server install |
| Deploy + health check | 1–2 min | `/health` must return 200 |
| **Total deploy time** | **~5–8 min** | |
| Post-deploy smoke tests | 15 min | Critical path verification |
| **Total Phase 2 time** | **~25–30 min** | |

---

## 11. Post-Deploy Smoke Tests

Execute these **in order** within 15 minutes of deploy completion.

### 11.1 Health & Availability (First 5 Minutes)

| # | Test | Expected Result | Command |
|---|------|-----------------|---------|
| 11.1.1 | Root health | `{"status":"ok"}` | `curl -s https://rekrutai.co/health` |
| 11.1.2 | API health | `{"status":"ok"}` | `curl -s https://rekrutai.co/api/health` |
| 11.1.3 | Homepage | 200 OK, hero visible | `curl -s https://rekrutai.co/` |
| 11.1.4 | Login page | 200 OK, form visible | `curl -s https://rekrutai.co/login` |
| 11.1.5 | Jobs API | Returns job data | `curl -s https://rekrutai.co/api/jobs?limit=1` |

### 11.2 Security Headers (Critical — Must Pass)

| # | Test | Expected | Command |
|---|------|----------|---------|
| 11.2.1 | `x-powered-by` | **ABSENT** | `curl -I https://rekrutai.co/health \| grep -i x-powered-by` (empty) |
| 11.2.2 | `permissions-policy` | `camera=(self), microphone=(self)` | `curl -I https://rekrutai.co/health \| grep -i permissions-policy` |
| 11.2.3 | `content-security-policy` | Present | `curl -I https://rekrutai.co/ \| grep -i content-security-policy` |
| 11.2.4 | `strict-transport-security` | `max-age=31536000` | `curl -I https://rekrutai.co/ \| grep -i strict-transport-security` |
| 11.2.5 | `x-frame-options` | `SAMEORIGIN` | `curl -I https://rekrutai.co/ \| grep -i x-frame-options` |
| 11.2.6 | `x-content-type-options` | `nosniff` | `curl -I https://rekrutai.co/ \| grep -i x-content-type-options` |

### 11.3 Functional Smoke Tests (Within 15 Minutes)

| # | Test | Steps | Expected Result |
|---|------|-------|-----------------|
| 11.3.1 | Homepage | Load `/`, check hero, features, pricing | All sections visible, no console errors |
| 11.3.2 | Login flow | Test credentials → login → dashboard | Login succeeds, redirects correctly |
| 11.3.3 | Candidate jobs | Navigate to `/candidate/jobs` | Job listings load, search/filter work |
| 11.3.4 | Recruiter dashboard | `/recruiter/dashboard` | Dashboard loads, analytics visible |
| 11.3.5 | Dark mode toggle | Click toggle on any page | Theme switches, persists on reload |
| 11.3.6 | Mobile responsive | Emulate iPhone 14 in DevTools | Layout adapts, no horizontal scroll |
| 11.3.7 | Stripe pricing | Load `/pricing` | Free / Pro / Enterprise tiers visible |
| 11.3.8 | API endpoints | `GET /api/jobs`, `GET /api/auth/me` | Returns expected data |
| 11.3.9 | Admin panel | Login with admin credentials | Admin dashboard accessible |
| 11.3.10 | AI coaching (if Polsia key set) | Start mock interview | AI response generated |

---

## 12. Rollback Plan

### 12.1 Fast Rollback: Render Dashboard (1–2 minutes)

1. Go to Render Dashboard → `rekrutai-prod` service
2. Click **"Manual Deploy"** → **"Deploy a specific commit"**
3. Select the last known good commit (note commit hash before deploy)
4. Wait for health check (`/health` returns 200)
5. Verify `curl -s https://rekrutai.co/health` returns `{"status":"ok"}`

### 12.2 Git Revert Rollback (2–5 minutes)

```bash
git checkout main
git revert -m 1 <NEW_BAD_COMMIT> --no-edit
git push origin main
# Then trigger manual deploy in Render Dashboard
```

### 12.3 Database Rollback (if data corruption)

1. Render Dashboard → `rekrutai-prod-db` → **Snapshots**
2. Select pre-deploy snapshot (taken in step 10.1.5)
3. Click **Restore**
4. Wait for restore (5–10 minutes)
5. Restart `rekrutai-prod` service

### 12.4 Rollback Triggers

| Condition | Action | ETA |
|-----------|--------|-----|
| `/health` returns non-200 for > 2 minutes | Immediate Render dashboard rollback | 1–2 min |
| 50%+ of smoke tests fail | Git revert + investigate | 2–5 min |
| Database errors in logs | DB snapshot restore + code revert | 10–15 min |
| Stripe payment failures | Disable Stripe webhooks + investigate | 5–10 min |
| AI provider circuit breakers tripped | Reset via `/api/ai-health/reset` (admin) | 2–5 min |

---

## 13. Appendix: Useful Commands

```bash
# === HEALTH CHECKS ===
# Production
curl -s https://rekrutai.co/health | jq .
curl -s https://rekrutai.co/api/health | jq .

# Staging
curl -s https://rekrutai-dev.onrender.com/health | jq .
curl -s https://rekrutai-staging.onrender.com/health | jq .

# === SECURITY HEADERS ===
curl -I https://rekrutai.co/curl -I https://rekrutai-dev.onrender.com/

# === BUILD ===
cd /root/.openclaw/workspace/Rekrut_AI_v2
cd client && npm install --include=dev && npm run build
cd .. && node -c server.js
for f in routes/*.js; do node -c "$f"; done

# === GIT ===
git status
git log --oneline -5 main
git log --oneline origin/main -5

# === E2E ===
cd /root/.openclaw/workspace/Rekrut_AI_v2
npx playwright test

# === MIGRATIONS ===
node migrate.js  # dry-run — check which are pending
# On prod (via Render Shell):
# psql $DATABASE_URL -c "SELECT * FROM _migrations ORDER BY id;"

# === BUNDLE SIZE ===
ls -lah client/dist/assets/

# === SSL CHECK ===
echo | openssl s_client -connect rekrutai.co:443 -servername rekrutai.co 2>/dev/null | openssl x509 -noout -dates -subject -issuer

# === DNS CHECK ===
dig +short rekrutai.co
dig +short www.rekrutai.co
```

---

## 14. Go / No-Go Verdict

### ⚠️ CURRENT VERDICT: CONDITIONAL NO-GO (as of 2026-06-09)

**Primary blockers:**
1. **Duplicate migration prefixes** (`003`, `005`, `035`, `040`) — non-deterministic ordering risk.
2. **Production DB snapshot not taken** — no safe rollback path.
3. **Stripe live keys missing** — no revenue capability in production.

**Secondary blockers:**
4. **Local `main` 2 commits ahead of `origin/main`** — need to push or verify if intended for prod.
5. **Untracked debug files in workspace** — clean up before deploy.
6. **E2E tests not confirmed on latest `main`** — need QA sign-off.

### Path to GO

| Step | Owner | Estimated Time | Cumulative |
|------|-------|----------------|------------|
| Rename duplicate migration prefixes | DO-001 | 30 min | 30 min |
| Clean untracked files | DO-001 | 15 min | 45 min |
| Push local `main` to `origin/main` (if intended) | DO-001 | 1 min | 46 min |
| Run E2E tests on latest `main` | QA | 2–4 hrs | 3–5 hrs |
| Ranga generates Stripe live keys + webhook | Ranga | 1–2 days | 1–2 days |
| Ranga sets all `sync: false` env vars in Render Dashboard | Ranga + DO-001 | 2–4 hrs | 1–2 days |
| Update OAuth redirect URIs (Google, LinkedIn) | Ranga | 30 min | 1–2 days |
| Take production DB snapshot | DO-001 | 15 min | 1–2 days |
| Ranga Go/No-Go approval | Ranga | 30 min | **1–2 days total** |
| **Execute deploy** | DO-001 | 25–30 min | — |

**Estimated time to GO:** **1–2 days** (primarily dependent on Ranga completing Stripe setup and secret configuration).

---

*Document version: 2026-06-09 v2.0 — Prepared by DevOps Automator*  
*This is a living document. Update as blockers are resolved and new issues are discovered.*
