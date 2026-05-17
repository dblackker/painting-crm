# Painting Contractor CRM & Job Costing Research
**Date:** 2026-05-17  
**Repo:** dblackker/painting-crm  
**Purpose:** Comprehensive market scan of CRM + job costing for painting contractors, plus gap analysis vs current Black Line painting-crm.

---

## 1. Market Landscape - CRM / Field Service

### 1.1 General Field Service Management

**Jobber**
- Pricing: Core $39/mo, Connect $119, Grow $199, Plus $599. Starts $29/mo solo. Annual saves ~40%.
- Value prop: All-in-one painting contractor software - estimating, scheduling, invoicing, CRM.
- Features: Client management, lead tracking, customizable quotes, time tracking, automated reminders, QuickBooks/Stripe integrations.
- Strengths: Affordable small teams, 4.6/5 G2, 200k+ pros.
- Gaps: Mobile app limits, scheduling gaps, integration issues reported.

**Housecall Pro**
- Pricing: Basic $59/mo, Essentials $149/mo, MAX $299/mo.
- Value prop: Home service pros with stronger marketing & sales tools, scalable.
- Features: Estimates, GPS tracking, client management, AI job booking, mobile payments, built-in marketing.
- Gaps: Price jumps with add-ons, mobile glitches, job costing & QBO integration criticised.

**ServiceTitan**
- Pricing: $245-$500 / technician / mo. Implementation $5k-$50k+.
- Value prop: Enterprise platform with CRM, scheduling, real-time financial tracking.
- Features: Customer portals, marketing automation, sales automation.
- Gaps: Overkill for <10 techs, steep learning curve, limited estimating.

**Pipedrive**
- Pricing: Lite → Ultimate from $14/mo.
- Value prop: Pure sales pipeline CRM.
- Gaps: Not field-service native; needs add-ons for scheduling/dispatch.

**Houzz Pro**
- Value prop: Lead management, video consults, cloud file sharing for painting contractors.

### 1.2 Painting-Native Sales/CRM

**PaintScout**
- Pricing: Core $99/mo + CRM add-on $42/mo (listings $79/user/mo).
- Value prop: Sales platform; 23% win rate lift, 2B+ sold, 4.9★.
- Features: Production-rate estimating, automated follow-ups, customizable estimates, e-sign, invoicing, CRM pipeline, event calendar.
- Limit: No API, reports/Zapier not yet.

**DripJobs**
- Pricing: $97/mo.
- Value prop: All-in-one CRM automating follow-ups, proposals, invoicing, scheduling.
- Features: 40+ pre-written drip messages, AI sales assist, 2-way texting, Good/Better/Best packages.
- Integration: Zapier → PaintScout for estimate scheduling → quote creation.

**QuoteIQ**
- Pricing: From $29.99/mo.
- Value prop: AI estimating, automated reviews, GPS tracking, 50+ templates. 60-92% cheaper than Jobber/ServiceTitan.
- Rating: 4.7★ 4,103 reviews.

---

## 2. Job Costing Tools

Benchmarks (Knowify): Gross profit 45%+, labor 40%, material 15%.

**PaintScout** – See above. Estimating-centric.

**JobTread**
- Pricing: $159/mo annual / $199 mo.
- Features: CRM, estimating, project management, budgeting. QuickBooks/Slack.
- Costing: Cost catalog with items, assemblies, templates for automated pricing.

**CoConstruct**
- Pricing: $99-$499/mo.
- Features: Financial system tracks accounting codes, estimate scope, trade partner bids, proposals.
- Gaps: Mobile limits, export challenges.

**Knowify**
- Pricing: Core $99/mo + $10/user/mo.
- Features: Real-time cost tracking, scheduling, invoicing, QuickBooks integration.
- Costing: Phase-specific cost analysis, precise budgeting.
- Content: Painting estimating guides, job costing tips.

**STACK**
- Estimating & takeoff for painting: digital takeoffs, material/labor calc, bid management.

---

## 3. Feature Matrix

| Platform | CRM | Estimating | Scheduling | Invoicing | Automation | Job Costing |
|----------|-----|------------|------------|-----------|------------|-------------|
| Jobber | Yes | Quotes | Yes | Yes | Reminders | Basic |
| Housecall Pro | Yes | Yes | GPS | Mobile pay | Marketing AI | Limited |
| ServiceTitan | Yes | Limited | Yes | Yes | Marketing | Real-time financials |
| PaintScout | Add-on | Production rates | No | Yes | Follow-ups | Estimating focus |
| DripJobs | Yes | Packages | Calendar | Proposals | Drips + AI | No |
| JobTread | Yes | Yes | PM | Yes | No | Cost catalog |
| Knowify | Yes | Yes | Yes | Yes | No | Real-time phase costing |

---

## 4. Current Black Line painting-crm Status

From MEMORY.md as of 2026-05-16/17:

**Deployed:** crm.blacklinepainting.com on EC2 t3.small + Cloudflare Tunnel
**Stack:** Astro 4 SSR, Express + Prisma, SQLite dev (migratable to Supabase Postgres)
**Auth:** Cookie-based (blackline2024 default), 7-day session, middleware protects /admin
**UI:** CSS design tokens, AdminLayout with mobile bottom nav, mobile-first typography 13-24px, 44px touch targets
**Features live:**
- Login page, jobs page, more page with logout
- Website webhook integration: POST /api/leads → creates clients/leads
- Estimates hub at /admin/estimates with status filtering & client search
- GET /api/estimates endpoint
- Keyboard shortcuts Cmd+K / Cmd+N
- Contact form email infra with CONTACT_EMAILS env, MailChannels
- Google Drive photo sync with WebP optimization

**In progress / gaps:**
- Contract PDF export ~90% complete
- API proxy body forwarding & PDF streaming fixed May 14
- GitHub Actions health checks failing 502
- TypeScript @types missing
- EC2 IP drift noted
- No production-rate estimating engine
- No automated drip follow-ups
- No job costing module (labor/materials/overhead tracking)
- No QuickBooks/Square integration yet
- No GPS/time tracking
- No review automation

---

## 5. Gap Analysis vs Market

| Capability | Market Standard | painting-crm Today | Gap |
|------------|-----------------|-------------------|-----|
| Lead intake + pipeline | Jobber, DripJobs, PaintScout | Webhook creates leads, basic list | No pipeline stages, drag-drop, lost reason |
| Estimating | PaintScout production rates, Good/Better/Best | Estimate editor exists | No production rate DB, no package tiers, no e-sign |
| Automated follow-up | DripJobs 40+ drips, QuoteIQ automation | None | No drip sequences, SMS/email cadence |
| Scheduling / dispatch | Jobber, HCP, ServiceTitan | Jobs page placeholder | No calendar, crew assignment, route optimization |
| Invoicing / payments | All | None | No invoices, no online payments |
| Job costing | Knowify real-time, JobTread cost catalog | None | No labor/material entry, no budget vs actual |
| QuickBooks sync | Jobber, HCP, Knowify, JobTread | Discussed, not implemented | No sync |
| Reviews / reputation | QuoteIQ automated reviews | None | No review requests |
| Mobile app / GPS | HCP, ServiceTitan | Responsive web only | No offline, no GPS punch |
| Reporting | ServiceTitan real-time financials | None | No dashboards, KPIs |

---

## 6. Recommendations – Build vs Buy vs Integrate

**Keep core CRM custom** (you value owner-controlled scheduling, data ownership).

**Integrate specialist tools:**
1. **Estimating:** Embed PaintScout or build production-rate engine. PaintScout $99/mo saves build time; API not available → use Zapier via DripJobs.
2. **Drip follow-up:** DripJobs $97/mo for automated SMS/email sequences. Alternative: build lightweight drip engine in CRM using cron + Twilio (paired device SMS first).
3. **Job costing:** Add module to painting-crm:
   - Cost catalog table (materials, labor rates, assemblies)
   - Job budget vs actual tracking
   - Time entries per crew member
   - QuickBooks Online sync via API
   Start with Knowify-style phase costing, not full ERP.
4. **Scheduling:** Build calendar view with “Propose 3, Owner Confirms” flow already specced in blackline-ops-agents.
5. **Payments:** Stripe Checkout for deposits + final payments; webhook → CRM.

**Cost stack if buying:**
- PaintScout Core + CRM: $141/mo
- DripJobs: $97/mo
- QuickBooks: ~$90/mo
≈ $328/mo vs ServiceTitan $245/tech

**Cost if building on painting-crm:**
- Dev time for costing + drips + scheduling
- Hosting already covered (EC2 t3.small)
- SMS via paired device = $0 initially

---

## 7. Next Research Tasks

- [ ] Map PaintScout production rates to Black Line historical jobs
- [ ] Define job costing data model in Prisma (Jobs, Budgets, CostItems, TimeEntries, Expenses)
- [ ] Spec drip sequence content for painting sales cycle (lead → estimate → follow-up → close → review)
- [ ] Evaluate QuickBooks Online API scopes needed for job costing sync
- [ ] Prototype Good/Better/Best package builder UI
- [ ] Test MailChannels deliverability after SPF merge

---

## 8. Sources

Search results 2026-05-17:
- Jobber pricing & features
- Housecall Pro pricing
- ServiceTitan pricing
- PaintScout pricing
- DripJobs pricing
- JobTread pricing
- CoConstruct pricing
- Knowify pricing

Citations captured in agent context.

---

*Generated by Hatch research agent for Black Line Painting LLC CRM strategy.*
