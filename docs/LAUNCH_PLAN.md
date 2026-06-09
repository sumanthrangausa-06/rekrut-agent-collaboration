# HireLoop — 90-Day Launch Plan

**Launch Date:** August 15, 2026  
**Pre-Launch Date:** July 15, 2026 (soft launch for beta users)  
**Product:** HireLoop — AI-native recruitment platform  
**Tagline:** *"The way hiring should have been built from day one."*

---

## Phase 1: Foundation (June 5-19, 2026) — Sprint 0

### Goal: Fix the foundation. Nothing ships on a broken foundation.

### Security (Week 1-2)
- [ ] Fix CORS to allow only production domains
- [ ] Add secure flag to session cookies (HTTPS only)
- [ ] Implement CSRF protection for all state-changing endpoints
- [ ] Fix document IDOR vulnerability (recruiters can only view their own applicants' documents)
- [ ] Add CSP headers + Helmet middleware
- [ ] Sanitize error messages in production (no stack traces, no file paths)
- [ ] Enable HSTS (HTTP Strict Transport Security)
- [ ] Add rate limiting to admin endpoints (currently unprotected)
- [ ] Security scan with OWASP ZAP — zero critical findings required
- [ ] Penetration testing (internal QA team + external bounty program)

### Infrastructure (Week 1-2)
- [ ] Set up staging environment (separate from dev)
- [ ] Automated backup verification (daily)
- [ ] Database read replicas for reporting queries
- [ ] CDN setup for static assets (Cloudflare already active, optimize config)
- [ ] Monitoring: Datadog + Sentry + PagerDuty integration
- [ ] Alerting: P0 alerts to CTO + CEO, P1 to team leads
- [ ] Log aggregation: Structured JSON logging, centralized log storage
- [ ] Health check endpoint improvements: database connectivity, AI provider status, disk space
- [ ] Disaster recovery runbook: RTO < 1 hour, RPO < 15 minutes

### Email Notifications (Week 2)
- [ ] Email service queue (BullMQ + Redis, or PostgreSQL-backed queue)
- [ ] Transactional email templates: 15 templates (application, interview, offer, document, reminder, etc.)
- [ ] SMTP provider setup (SendGrid/Postmark/Amazon SES)
- [ ] Email deliverability: SPF, DKIM, DMARC records
- [ ] Bounce handling, unsubscribe management
- [ ] Email analytics: open rates, click rates, bounce rates
- [ ] Integration: trigger emails from all key events (application, interview, offer, etc.)

### Testing (Week 1-2)
- [ ] E2E test suite (Playwright or Cypress)
- [ ] API test suite (Postman + Newman CI/CD)
- [ ] Security test suite (OWASP ZAP automation)
- [ ] Load testing (k6 or Artillery)
- [ ] AI regression tests (benchmark prompt outputs, detect drift)
- [ ] Test data factory (seed data for all test environments)
- [ ] CI/CD pipeline: run all tests on every PR, block merge on failure

### Metrics
- **Security:** Zero critical findings, zero P1 findings
- **Testing:** 80%+ code coverage, all E2E flows passing
- **Email:** 99%+ deliverability, < 1% bounce rate
- **Infrastructure:** 99.9% uptime target, < 30 min MTTR

---

## Phase 2: Compliance & Enterprise Readiness (June 19 - July 3, 2026) — Sprint 1

### Goal: Enterprise sales depend on this. August 2026 EU AI Act deadline.

### EU AI Act Compliance Dashboard (Week 3-4)
- [ ] Bias audit dashboard (per-job, per-company, per-demographic)
  - Demographic breakdown: gender, ethnicity, age, disability
  - Disparate impact analysis: 4/5ths rule compliance
  - Bias report generation: PDF export for auditors
- [ ] Consent management UI
  - Candidate can view all AI decisions made about them
  - Candidate can modify consent preferences
  - Candidate can revoke consent (data deletion workflow)
  - Consent audit trail (who consented, when, for what)
- [ ] Data lineage tracking
  - For every AI score: which model, which prompt version, which provider
  - Full traceability: input → processing → output → decision
  - Version history: what changed between versions
- [ ] Explainability panel
  - For every AI decision: human-readable explanation
  - Interview score: "Your score was 7.2/10 because you demonstrated strong technical knowledge but had 3 filler words per minute."
  - Match score: "You matched 85% because you have 4/5 required skills and 3 years of relevant experience."
  - Screening result: "You were ranked #3 because your Python score (92%) and system design score (88%) exceeded the job requirements."
- [ ] Human-in-the-loop review
  - Recruiter can override any AI decision
  - Override reason required, logged in audit trail
  - Appeals process: candidate can appeal any score
- [ ] AI system registration (EU AI Act Article 12)
  - System risk classification: high-risk (employment decisions)
  - Technical documentation: system design, training data, performance metrics
  - Quality management system: monitoring, logging, incident reporting
  - Human oversight: meaningful human review, override capability

### SOC2 Type I Preparation (Week 3-4)
- [ ] Security controls documentation (25 controls)
- [ ] Access control policies: least privilege, role-based, quarterly review
- [ ] Change management: all changes documented, tested, approved
- [ ] Incident response plan: 24-hour response, 72-hour resolution target
- [ ] Vendor management: vendor risk assessment, security review
- [ ] Data classification: public, internal, confidential, restricted
- [ ] Encryption: data at rest (AES-256), data in transit (TLS 1.3)
- [ ] Backup and recovery: daily backups, weekly restore tests
- [ ] Logging and monitoring: all access logged, anomalies detected
- [ ] Employee security training (agent training module)

### Analytics & Insights (Week 3-4)
- [ ] Recruiter funnel analytics
  - Post → View → Apply → Screen → Interview → Offer → Hire
  - Conversion rates at each stage
  - Time spent at each stage
  - Drop-off reasons (qualitative feedback)
- [ ] Time-to-hire tracking
  - Average time-to-hire by role, company, recruiter
  - Benchmarking: industry averages (Greenhouse, Lever data)
- [ ] Candidate quality by source
  - Which job boards produce the best candidates?
  - Which referral sources are most effective?
  - Cost per quality hire by source
- [ ] Revenue dashboard
  - MRR, ARR, churn, LTV, CAC
  - Revenue by plan, by company size, by industry
  - Forecasting: pipeline, expected revenue, churn risk
- [ ] Admin dashboard v2
  - System health: uptime, error rates, AI provider status
  - Financial health: revenue, costs, margins, runway
  - User health: DAU, MAU, activation, retention, churn
  - AI health: token usage, cost per user, provider performance

### Recruiter AI Screener (Week 4)
- [ ] Structured candidate evaluation against job requirements
  - Recruiter inputs: required skills, experience level, culture fit criteria
  - AI analyzes: resume, assessment scores, interview scores, behavior
  - Output: ranked shortlist with scoring rationale
- [ ] Custom screening criteria per job
  - Weighted criteria: technical skills (40%), experience (30%), culture fit (20%), communication (10%)
  - Minimum thresholds: "Must score > 70% on technical assessment"
  - Custom questions: recruiter can add company-specific screening questions
- [ ] Screening summary reports
  - PDF export for hiring managers
  - Comparative analysis: candidate A vs candidate B
  - Red flags: missing skills, experience gaps, behavior concerns
- [ ] Batch processing
  - Screen 100+ applicants at once
  - Bulk actions: reject, advance to interview, request more info
  - Queue management: priority scoring, deadline tracking

### Metrics
- **Compliance:** EU AI Act dashboard complete, SOC2 controls documented
- **Analytics:** 15+ metrics tracked, 5+ dashboards live
- **Screener:** Recruiter can screen 50 applicants in < 10 minutes

---

## Phase 3: Differentiation & Growth (July 3-17, 2026) — Sprint 2

### Goal: Features that make us impossible to ignore.

### Advanced OmniScore v2 (Week 5-6)
- [ ] Multi-factor scoring with 8 dimensions (up from 4)
  - Interview performance (200 pts)
  - Technical ability (200 pts)
  - Resume quality (200 pts)
  - Platform behavior (250 pts)
  - **NEW: Communication skills** (100 pts) — email quality, message clarity
  - **NEW: Cultural fit** (100 pts) — values alignment, team compatibility
  - **NEW: Growth potential** (100 pts) — learning velocity, adaptability
  - **NEW: Reference quality** (100 pts) — verified references, recommendation strength
- [ ] Score explainability v2
  - Interactive breakdown: hover over any score component for detailed explanation
  - "What if" simulator: candidate can see how improving X would affect their score
  - Peer comparison: "You scored higher than 73% of candidates for this role"
  - Trend analysis: score history over time, improvement trajectory
- [ ] Appeals process v2
  - Formal appeal submission with evidence
  - Human review panel (3 reviewers, majority vote)
  - 48-hour response time SLA
  - Appeal outcome: upheld, rejected, partial adjustment
- [ ] Role-specific scoring v2
  - 50+ role templates (software engineer, data scientist, product manager, etc.)
  - Custom weighting per role: frontend engineer weights technical skills higher than PM
  - Industry calibration: finance roles require different skills than startup roles
  - Seniority levels: junior, mid, senior, staff, principal

### Calendar Integration (Week 5-6)
- [ ] Google Calendar integration
  - OAuth 2.0 connection
  - Interview scheduling: find mutual availability, send calendar invites
  - Recruiter calendar: block interview slots, send availability links
  - Reminder automation: 24h, 1h, 15min before interview
- [ ] Outlook/Office 365 integration
  - Same features as Google Calendar
  - Enterprise IT compatibility
- [ ] Scheduling optimization
  - AI-suggested best times (candidate energy, recruiter availability, timezone)
  - Timezone handling: all times displayed in user's local timezone
  - Buffer management: prevent back-to-back interviews, lunch breaks
- [ ] Calendar analytics
  - Interview scheduling efficiency: average time to schedule
  - No-show rates: by reminder type, by candidate score
  - Optimal interview times: when do candidates perform best?

### Candidate AI Career Coach (Week 6)
- [ ] Skill gap analysis
  - Compare candidate skills to target role requirements
  - Identify missing skills, recommend learning resources
  - LinkedIn Learning, Coursera, Udemy integration
  - Progress tracking: candidate marks skills as learned, AI updates score
- [ ] Career path recommendations
  - Based on current skills and experience, suggest next roles
  - Salary trajectory: expected salary at each career stage
  - Alternative paths: "You could also be a product manager with 2 more skills"
- [ ] Interview preparation
  - Role-specific practice questions
  - Company-specific research (culture, values, interview style)
  - Mock interview with AI feedback
  - Improvement tracking: score improvement over time
- [ ] Job application strategy
  - Which jobs should you apply to? (match score > 80%)
  - Application timing: when to apply for maximum visibility
  - Follow-up reminders: 1 week, 2 weeks after application
  - Rejection recovery: learn from rejection, improve for next time

### Metrics
- **OmniScore:** 8 dimensions, 50+ role templates, appeals process
- **Calendar:** Google + Outlook, 2-week scheduling, 24h reminders
- **Career Coach:** Skill gap analysis, career paths, interview prep
- **User engagement:** 3x increase in candidate session time

---

## Phase 4: Scale & Launch (July 17 - August 15, 2026) — Sprint 3

### Goal: Ship it. Launch it. Scale it.

### ATS Integrations (Week 7-8)
- [ ] Greenhouse integration
  - OAuth connection, webhook events
  - Bi-directional sync: jobs, candidates, applications, interviews
  - Candidate import: bulk import from Greenhouse
  - Export: send HireLoop candidates to Greenhouse
  - Status sync: when candidate advances in HireLoop, update Greenhouse
- [ ] Lever integration
  - Same features as Greenhouse
  - Lever-specific: opportunity stages, feedback forms
- [ ] Workday integration (enterprise)
  - SOAP API integration
  - HRIS data sync: employees, org structure, job requisitions
  - Single sign-on (SSO): SAML 2.0
- [ ] API documentation v1
  - OpenAPI 3.0 spec
  - Developer portal: docs, SDKs, examples
  - Webhooks: candidate events, job events, interview events
  - Rate limits: 1000 requests/minute per API key
  - Authentication: API keys + OAuth 2.0

### Performance & Scale (Week 7-8)
- [ ] Frontend caching layer
  - React Query or SWR for data fetching
  - Stale-while-revalidate: show cached data, refresh in background
  - Optimistic updates: UI updates before API confirms
  - Request deduplication: same request in flight? return same promise
- [ ] Backend caching layer
  - Redis for session storage, rate limiting, job queues
  - Database query caching: frequently-read queries cached for 5 minutes
  - AI response caching: identical prompts cached for 1 hour
  - CDN caching: static assets cached for 1 year
- [ ] Database optimization
  - Read replicas for analytics queries
  - Partitioning: large tables partitioned by date
  - Connection pooling: increase pool size for scale
  - Query optimization: eliminate N+1 queries, add missing indexes
- [ ] Load testing & capacity planning
  - 1000 concurrent users: < 2s p95 response time
  - 10,000 concurrent users: < 3s p95 response time
  - AI provider capacity: fallback chain handles 10x load spikes
  - Database capacity: 100k queries/minute without degradation

### Marketing Launch (Week 7-8)
- [ ] Brand launch: "HireLoop" rebrand from Rekrut AI
  - New landing page, new logo, new color scheme
  - Brand story: "Built from day one as AI-native"
  - Value proposition: "Hire faster, hire fairer, hire smarter"
- [ ] Content marketing blitz
  - 10 blog posts: "The Future of AI in Hiring", "How OmniScore Works", "EU AI Act Guide"
  - 5 case studies: early customers, measurable results
  - 3 whitepapers: "AI-Native Recruitment", "Bias-Free Hiring", "The Future of Work"
  - 2 webinars: "How to Build an AI-Native ATS" (with CTO Suga)
- [ ] PR campaign
  - TechCrunch exclusive: "HireLoop launches AI-native recruitment platform"
  - Product Hunt launch: #1 Product of the Day target
  - LinkedIn thought leadership: CEO posts, employee advocacy
  - Podcast tour: 5 podcast appearances (How I Built This, etc.)
- [ ] Paid acquisition
  - Google Ads: "AI recruitment platform", "hire faster with AI"
  - LinkedIn Ads: targeting HR directors, VPs of People
  - Retargeting: pixel on website, ads for visitors who didn't sign up
  - Budget: $10k/month for paid acquisition

### Sales & Partnerships (Week 7-8)
- [ ] Enterprise outreach (first 50 prospects)
  - Target: companies with 500+ employees, active hiring
  - Channels: LinkedIn, referrals, cold email, events
  - Pitch: EU AI Act compliance + OmniScore + AI-native approach
  - Demo: live demo with prospect's own job descriptions
- [ ] Partnership program
  - ATS partners: Greenhouse, Lever, Workday (co-marketing)
  - HRIS partners: BambooHR, Gusto, ADP (integration + referral)
  - Consulting partners: HR consulting firms (revenue share)
  - API partners: background check providers, assessment platforms
- [ ] Pricing & packaging finalization
  - Free tier: 1 job posting, 5 candidates, basic matching
  - Pro tier: $199/month: 10 jobs, unlimited candidates, AI screening
  - Business tier: $599/month: 50 jobs, ATS integrations, analytics
  - Enterprise tier: custom pricing: unlimited, API access, dedicated support, SOC2
  - Annual discount: 2 months free

### Launch Day (August 15, 2026)
- [ ] Morning: Team standup, final checks, rollback plan ready
- [ ] 10:00 UTC: Deploy to production (blue-green deployment)
- [ ] 10:30 UTC: Monitor dashboards, error rates, AI provider health
- [ ] 11:00 UTC: Product Hunt launch, social media blast
- [ ] 12:00 UTC: PR articles go live (TechCrunch, etc.)
- [ ] 14:00 UTC: Live demo webinar (register 1000+ attendees)
- [ ] 16:00 UTC: First customer signups, revenue tracking
- [ ] 18:00 UTC: Retrospective: what went well, what didn't, lessons learned
- [ ] 20:00 UTC: Celebration (virtual team party)
- [ ] 24/7: On-call rotation for first 48 hours

---

## Post-Launch (August 15+ , 2026)

### Week 1 (Aug 15-22): Stabilize
- [ ] Monitor error rates, fix any critical bugs immediately
- [ ] Collect user feedback, prioritize top 10 requests
- [ ] Analyze funnel: where are users dropping off?
- [ ] First revenue: celebrate first paying customer
- [ ] Media follow-up: respond to press, social media

### Week 2-4 (Aug 22 - Sep 12): Iterate
- [ ] Rapid iteration: ship small improvements daily
- [ ] Feature requests: top 10 from feedback, prioritize
- [ ] Performance: optimize based on real usage patterns
- [ ] Growth: double down on channels that work, kill ones that don't
- [ ] Team: hire first 2 human engineers (backend + AI/ML)

### Month 2-3 (Sep 12 - Nov 12): Scale
- [ ] Series A fundraising: target $5M at $20M valuation
- [ ] Team expansion: hire 10 more engineers, 3 sales, 2 marketing
- [ ] International expansion: EU market (GDPR compliant), UK market
- [ ] Product expansion: AI career coach for universities, AI staffing for agencies
- [ ] Partnerships: 5 ATS integrations, 10 HRIS integrations

### Month 4-6 (Nov 12 - Feb 12, 2027): Dominate
- [ ] 1000 paying customers
- [ ] $1M ARR (annual recurring revenue)
- [ ] 50+ enterprise customers
- [ ] IPO preparation: financial controls, board expansion, legal compliance
- [ ] AI research: publish 2 papers, file 3 patents
- [ ] Community: 10,000 members in HireLoop community
- [ ] Awards: "Best HR Tech Startup", "Most Innovative AI Product"

---

## Risk Register

| Risk | Probability | Impact | Mitigation |
|------|------------|--------|------------|
| EU AI Act compliance delay | Medium | Critical | Start now, hire legal consultant, parallel workstreams |
| AI provider outage | Medium | High | Multi-provider fallback, circuit breakers, self-hosted models |
| Security breach | Low | Critical | Security-first culture, continuous monitoring, bug bounty |
| Competitor launch | Medium | Medium | Speed to market, differentiation, OmniScore moat |
| Funding delay | Medium | Medium | Bootstrap revenue, keep burn low, angel backup |
| Technical debt | High | Medium | Refactor weekly, monolith splitting, code review strictness |
| Team burnout | Medium | High | Sustainable pace, rotation, mental health, celebration |
| Customer churn | Medium | High | Customer success, NPS tracking, rapid iteration, stickiness |

---

## Budget (90 Days)

| Category | Amount | Notes |
|----------|--------|-------|
| Infrastructure | $5,000 | Render, Neon, Cloudflare, monitoring |
| AI Providers | $10,000 | OpenAI, Anthropic, NVIDIA, Groq, Cerebras |
| Marketing | $15,000 | PR, ads, content, events |
| Compliance | $5,000 | Legal consultant, SOC2 audit prep |
| Tools & Services | $3,000 | GitHub, Datadog, Sentry, etc. |
| Contingency | $5,000 | Buffer for unexpected costs |
| **Total** | **$43,000** | 90-day runway |

---

## Success Metrics (90-Day Targets)

| Metric | Target | Current | Gap |
|--------|--------|---------|-----|
| Daily Active Users (DAU) | 500 | ~50 | +450 |
| Monthly Recurring Revenue (MRR) | $5,000 | $0 | +$5,000 |
| Paying Customers | 25 | 0 | +25 |
| Enterprise Trials | 10 | 0 | +10 |
| API Calls / Day | 100,000 | ~5,000 | +95,000 |
| AI Uptime | 99.9% | ~99% | +0.9% |
| Security Findings (Critical) | 0 | 6 | -6 |
| Test Coverage | 80% | ~0% | +80% |
| NPS Score | 50 | N/A | Launch + track |
| Churn Rate (Monthly) | < 3% | N/A | Launch + track |
| Time-to-Hire (Avg) | 14 days | 30+ | -16 days |
| Candidate Quality Score | 750 | 650 | +100 |

---

## Daily Standup Format (All Agents)

### Yesterday:
- What did I complete?
- What blockers did I encounter?
- What did I learn?

### Today:
- What am I working on?
- What do I need from other teams?
- What risks do I see?

### This Sprint:
- Are we on track to hit sprint goals?
- What should we cut if behind?
- What should we add if ahead?

---

## Document Owner: Suga (CTO)  
## Last Updated: 2026-06-05  
## Next Review: Daily (standup), Weekly (sprint review), Monthly (strategic)
