# PaintFlow Implementation Plan v0.2
**Date:** 2026-05-17  
**Status:** Revised after PM + Senior Eng critique

## 0. Executive Summary

PaintFlow is a Cloudflare-first multi-tenant SaaS for solopreneur painting contractors. MVP ships in 10 weeks with P0: Leads, Estimating, Follow-up drips, Scheduling, Invoicing, Job Costing, 2-way SMS.

Differentiator: Real-time job costing + automated follow-ups built-in, not bolted on.

---

## 1. Product Requirements (PRD)

### 1.1 ICP & Problem
Solopreneur painters $200-600k revenue. Current stack: Jobber + PaintScout + QuickBooks + spreadsheets. Pain: follow-up leakage, no job profit visibility, too many apps.

### 1.2 MVP User Stories

**Onboarding**
- As owner, I can sign up with email magic link, create org, and send first estimate <10 min
- I can import Jobber CSV or skip

**Leads**
- Add lead manually or via embed form
- Pipeline: New → Contacted → Estimate Sent → Won → Lost
- Tap to call/text from mobile

**Estimating**
- Create Good/Better/Best packages with production rates
- Add photos, send via SMS/email link
- Client accepts + e-signs

**Follow-up Drips (P0)**
- Auto SMS Day 1, Email Day 3, SMS Day 7 after estimate sent
- Stop on accept/decline
- 2-way SMS inbox inside app

**Scheduling**
- Week calendar, drag job to day
- Google Calendar 1-way sync

**Invoicing**
- Create invoice from estimate
- Stripe Checkout link, 50% deposit default
- Mark paid

**Job Costing**
- Budget auto-created from accepted estimate
- Log time entries + expense receipts with photo
- Real-time Budget vs Actual + Margin %

**Customer Portal**
- Public estimate link: view, accept, pay

### 1.3 Out of MVP
- QuickBooks 2-way sync (P1)
- Review requests (P1)
- Offline PWA sync (P1)
- Crew mobile app (P2)

### 1.4 Success Metrics
- 10 paying orgs in 30 days post-launch
- <10 min time-to-first-estimate
- 70% estimate close rate
- 80% jobs have costing data

---

## 2. Technical Architecture

### 2.1 Stack Decision (ADR-001)

**Chosen:**
- Frontend: Astro 5 + React, Tailwind, shadcn, PWA
- API: Cloudflare Workers with Hono
- DB: Neon Postgres + Drizzle ORM (Workers-compatible)
- Auth: Better Auth with Workers KV sessions
- Storage: Cloudflare R2
- Payments: Stripe
- SMS/Email: Twilio + Resend
- Analytics: PostHog
- Hosting: Cloudflare Pages + Workers

**Rejected:** Prisma on Workers (needs Accelerate, cold start issues)

### 2.2 Multi-Tenancy

- All tables have `org_id`
- RLS policies: `USING (org_id = current_setting('app.current_org_id')::uuid)`
- Middleware sets org_id from session on every request
- Automated CI test attempts cross-org access → must fail

### 2.3 System Diagram

```
User → Cloudflare CDN → Pages (Astro PWA)
                  ↓
            Workers API (Hono)
              ├─ Neon DB via Hyperdrive
              ├─ R2 (signed URLs)
              ├─ Stripe
              ├─ Twilio/Resend
              └─ Queues (drips, PDFs)
```

### 2.4 Directory Structure

```
/paintflow
  /apps
    /web        # Astro PWA
    /api        # Workers Hono
  /packages
    /db         # Drizzle schema + migrations
    /ui         # shared components
    /analytics  # PostHog wrappers
  /infra       # Wrangler configs
  AGENTS.md
  CLAUDE.md
```

Each app has `CLAUDE.md` with conventions.

---

## 3. Data Model

**Core tables:**
- organizations(id, slug, name, plan)
- users(id, email)
- memberships(user_id, org_id, role)
- leads(id, org_id, status, source)
- estimates(id, org_id, lead_id, packages_json, status)
- jobs(id, org_id, estimate_id, scheduled_at)
- invoices(id, org_id, job_id, stripe_id, total)
- job_costs(id, org_id, job_id, budget_json, actual_json)
- time_entries(id, org_id, job_id, hours, task)
- expenses(id, org_id, job_id, amount, receipt_url)
- messages(id, org_id, lead_id, direction, body) # SMS
- drip_enrollments(id, org_id, estimate_id, step, scheduled_at)

Indexes on org_id, status, scheduled_at.

---

## 4. API Design

Base: `/api/v1`

Auth via cookie session, org_id in JWT.

Endpoints:
- `POST /leads`
- `GET /leads?status=`
- `POST /estimates`
- `POST /estimates/:id/send`
- `POST /estimates/:id/accept` (public)
- `POST /jobs`
- `POST /time-entries`
- `POST /expenses` (multipart)
- `GET /jobs/:id/costing`
- `POST /messages` (send SMS)
- `GET /messages?lead_id=`

All POSTs accept `Idempotency-Key` header.

---

## 5. Follow-up Drip Engine

**Trigger:** estimate.status = sent

**Steps:**
1. T+1 day SMS: "Hi {name}, quick check on estimate..."
2. T+3 day Email
3. T+7 day SMS final nudge

Implementation:
- Cloudflare Cron every 15 min → enqueues due steps
- Queue consumer sends via Twilio/Resend
- Stop condition: estimate accepted/declined

---

## 6. Job Costing Flow

1. Estimate accepted → create job_costs with budget from packages
2. Crew logs time via mobile: job → +Time
3. Expense: camera → R2 upload → create expense
4. Dashboard calculates: actual labor = sum(hours * rate), margin % = (invoice - actual)/invoice
5. Flag if actual >90% budget

---

## 7. Infrastructure & DevOps

### 7.1 Environments
- Production: `paintflow.app`
- Preview: PR → `pr-123.paintflow.pages.dev` + Neon branch
- Dev: local with `wrangler dev` + Neon dev branch

### 7.2 CI/CD (GitHub Actions)

Pipeline:
1. Lint + typecheck
2. Unit tests
3. Create Neon branch
4. Run Drizzle migrations
5. Deploy Workers + Pages preview
6. Playwright e2e against preview
7. Merge → deploy prod → run migrations → smoke test

Secrets via GitHub OIDC → Cloudflare, no long-lived tokens.

### 7.3 Observability

- Structured JSON logs with trace_id
- Cloudflare Logs → Axiom
- Sentry for errors with source maps
- PostHog events: `estimate_sent`, `estimate_accepted`, `invoice_paid`, `job_closed`
- Uptime checks
- SLO: p95 API <300ms, error rate <0.5%

### 7.4 Security

- RLS enforced
- Rate limit: 100 req/min per org
- Input validation with Zod
- R2 signed URLs 15 min expiry
- Stripe webhook signature verify
- Audit log table
- Turnstile on signup

---

## 8. Analytics Event Taxonomy

**Funnel:** lead_created → estimate_sent → estimate_viewed → estimate_accepted → invoice_sent → invoice_paid

Events include org_id, user_id, value.

Revenue metrics from Stripe webhooks → PostHog.

---

## 9. Timeline (10 Weeks)

**Weeks 1-2: Foundation**
- Monorepo, Cloudflare setup, Drizzle + Neon + Hyperdrive
- Better Auth + org switching
- CI/CD with preview DB branches
- Base UI shell + PWA

**Weeks 3-4: Leads & Estimating**
- Lead CRUD + pipeline
- Estimate builder Good/Better/Best
- Public accept page + e-sign
- PDF generation via Queue + Browser Rendering

**Weeks 5-6: Follow-ups + SMS + Scheduling**
- Drip engine + Cron + Queues
- 2-way SMS inbox
- Week calendar + Google sync
- Customer portal

**Weeks 7-8: Invoicing + Job Costing**
- Stripe Checkout + webhooks
- Invoice from estimate
- Job costing budget/actual
- Time entries + expenses with R2 upload
- Margin dashboard

**Weeks 9-10: Hardening**
- Onboarding flow + import CSV
- E2E tests, performance tuning
- PostHog instrumentation
- Docs: AGENTS.md, CLAUDE.md, ADRs
- Beta with 5 design partners

---

## 10. Documentation for LLMs

**Root AGENTS.md:** How to run, deploy, architecture overview

**Per-app CLAUDE.md:**
- Coding conventions
- How to add API route
- How to add PostHog event
- Testing approach

**/docs/adr/**
- 001-cloudflare-first.md
- 002-drizzle-over-prisma.md
- 003-rls-multitenancy.md

**Domain Glossary:** Lead, Estimate, Job, Invoice lifecycle

---

## 11. Comparison vs Existing

| Feature | PaintFlow | Jobber | PaintScout | DripJobs |
|---------|-----------|--------|------------|----------|
| Edge hosting | Yes | No | No | No |
| Follow-up drips P0 | Yes | Basic | No | Yes |
| Real-time job costing | Yes | Basic | No | No |
| 2-way SMS inbox | Yes | Yes | No | Yes |
| Price target | $49 | $39-199 | $99+ | $97 |

Advantage: One app, edge speed, costing built-in.

---

## 12. Risks & Mitigations

- **Drizzle migration:** Spike week 1
- **RLS leak:** Automated tenant isolation tests in CI
- **SMS deliverability:** Start with Twilio, fallback to email
- **PDF CPU limits:** Queue + Browser Rendering
- **Onboarding friction:** Seed templates, import flow

---

## 13. Next Steps

1. Initialize monorepo with Turborepo
2. Provision Neon + Cloudflare resources
3. Implement auth + org switching
4. Build lead pipeline UI
5. Recruit 5 design partners from PaintTalk

---

*This plan incorporates PM critique: follow-ups moved to P0, 2-way SMS added, and Senior Eng fixes: Drizzle over Prisma, RLS safety, preview DBs, observability.*
