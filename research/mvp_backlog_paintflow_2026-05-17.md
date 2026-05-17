# PaintFlow — MVP & Backlog for Solopreneur Painting Contractors
**Date:** 2026-05-17  
**Vision:** One login, one price. Run the whole painting business from phone.

---

## Founder Thesis

Solopreneur painters don't need ServiceTitan. They need:
1. Leads → booked job in <5 mins
2. Estimate that sells itself
3. Get paid fast
4. Know if the job made money

Everything else is bloat. Multi-tenant from day 1 so we can onboard 100 painters without code changes.

---

## ICP

- 1-3 person painting crews, owner-operator
- $200k-$600k revenue
- iPhone primary device
- Currently using: Jobber/Housecall Pro + PaintScout + spreadsheets + Gmail
- Pain: Too many tools, double entry, no job costing

---

## MVP Scope — 6 Weeks to First Paying User

### P0 Must Have for Launch

**1. Auth & Tenancy**
- Email + magic link login
- Organization = tenant
- Invite crew member (max 3 on MVP plan)

**2. Lead Inbox**
- Web form embed + manual add
- Pipeline: New → Contacted → Estimate Sent → Won → Lost
- Single list view, tap to call/text

**3. Estimating v1**
- Good / Better / Best packages
- Production rates DB for interior/exterior (sq ft, linear ft)
- Add photos
- E-sign + accept button
- Send via SMS/email link

**4. Scheduling**
- Simple calendar week view
- Drag job to day
- Google Calendar 1-way sync

**5. Invoicing & Payments**
- Create invoice from estimate
- Stripe Checkout link
- Deposit 50% default
- Mark paid manually

**6. Job Costing v1**
- Budget: Labor hours, Materials $, Subs $
- Actual: Log hours + material receipts (photo)
- Job P&L: Revenue - Actual = Gross Profit %

**7. Mobile First**
- PWA, installable
- Thumb-friendly bottom nav: Leads, Calendar, Jobs, Money

**Out of scope for MVP:**
- Automated drips
- QuickBooks sync
- Route optimization
- Review requests
- Time clock GPS

---

## Pricing for MVP

**Solo: $49/mo**
- 1 user + 1 crew
- Unlimited leads/estimates
- Stripe fees pass-through

**Launch offer:** First 20 painters $29/mo locked

---

## Backlog — Prioritized

### P1 — Close More Jobs (Weeks 7-10)
- Drip follow-up engine: 7-day sequence after estimate sent
- SMS 2-way inbox inside app
- Estimate templates per service type
- Deposit auto-request on accept
- Lost reason tracking + report

### P2 — Get Paid Faster (Weeks 11-14)
- Card on file
- Payment plans
- QuickBooks Online sync (invoices + payments)
- Daily cash report

### P3 — Know Your Numbers (Weeks 15-18)
- Dashboard: Pipeline value, close rate, avg job size, gross margin
- Crew time clock with GPS punch
- Material catalog with supplier pricing
- Budget vs actual alerts

### P4 — Scale to Small Crew (Weeks 19-24)
- Crew app (limited permissions)
- Job checklist + photos
- Customer portal: view estimate, pay, schedule
- Review request automation
- Referral tracking

### P5 — Growth Engine
- Website widget: Instant ballpark estimator
- Google Local Services integration
- AI estimate helper: upload photos → auto measure
- Multi-location

---

## Data Model v1

**Organizations** (tenant)
**Users** (belongs_to org)
**Leads** (status, source, contact)
**Estimates** (packages jsonb, totals, status)
**Jobs** (scheduled_at, crew_id)
**Invoices** (stripe_payment_intent_id)
**JobCosts** (budget vs actual)
**TimeEntries** (user_id, job_id, hours)
**Expenses** (receipt_url, amount)

Multi-tenant via `org_id` on all tables + RLS.

---

## Tech Stack

- Frontend: Astro + React islands, Tailwind, shadcn
- Backend: Node + Express, Prisma, Postgres (Supabase)
- Auth: Lucia + magic links
- Payments: Stripe
- SMS: Twilio (start with paired device relay to save cost)
- File storage: Cloudflare R2
- Hosting: Fly.io or Railway

---

## Go-to-Market MVP

1. Build in public on Painting Contractors Facebook groups
2. Offer free migration from Jobber CSV
3. 5 design partner painters → build with them
4. Loom demo of 60-second lead → paid invoice flow

Success metric: 10 paying painters, $500 MRR, 70% estimate close rate, avg 45% gross margin tracked.

---

## Risks & Bets

- Bet: Solopreneurs will switch for all-in-one + job costing
- Risk: Estimating accuracy — solve with editable production rates
- Risk: Stripe holds — start with manual mark-paid option
- Moat: Painting-specific production rates + job costing built-in, not bolted on

---

## Next Actions

1. Scaffold multi-tenant repo with org_id RLS
2. Build Lead pipeline UI
3. Seed production rates DB from PaintScout benchmarks
4. Stripe Checkout prototype
5. Recruit 3 design partners from Black Line network

---

*This is the build plan. Ship MVP in 6 weeks, learn from real painters, iterate weekly.*
