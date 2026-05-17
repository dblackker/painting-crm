# PaintFlow — Job Costing P0 Spec
**Date:** 2026-05-17  
**Status:** P0 for MVP

## Why P0

Solopreneurs don't know if they made money until tax time. Job costing is the moat vs Jobber/HCP. If we ship without it, we're just another CRM.

## MVP Job Costing — What Ships Week 6

### Core Flow
1. When estimate is accepted → auto-create Job Budget
2. Crew logs time + materials on job
3. Dashboard shows Budget vs Actual + Gross Margin % in real time

### Data Captured
**Budget (from estimate)**
- Labor hours by task (prep, paint, trim)
- Labor cost = hours × crew rate
- Materials $ (paint, primer, supplies)
- Subs $
- Overhead % (default 10%)
- Target gross margin %

**Actual**
- Time entries: user, date, hours, task, notes
- Expenses: photo receipt, amount, category, vendor
- Materials used: qty × cost

**Calculated**
- Actual labor cost = sum hours × rate
- Actual total cost = labor + materials + subs + overhead
- Gross profit = invoice total - actual cost
- Gross margin % = gross profit / invoice total
- Variance = actual - budget per category

### UI

**Job Detail → Costing Tab**
- Top cards: Budget | Actual | Variance | Margin %
- Progress bar: % of budget burned
- Table:
  Category | Budget | Actual | Variance
  Labor | 20h / $800 | 18h / $720 | -$80
  Materials | $600 | $540 | -$60

**Quick Add**
- "+ Time" → hours, task picker
- "+ Expense" → camera opens, amount, category

**Daily Standup View**
- Jobs this week with margin color:
  Green >45%, Yellow 30-45%, Red <30%

### Rules
- Auto-flag job if actual > 90% of budget
- Can't close job until costing complete
- Export CSV for accountant

### Minimal Viable Accuracy
- Manual entry is fine for MVP. No integrations.
- Crew rate defaults from org settings, editable per entry
- Material categories: Paint, Primer, Supplies, Equipment Rental

### Multi-tenant
- All costing tables scoped by org_id
- Rates stored per org

## P1 Enhancements (Weeks 7-10)
- Cost catalog with supplier pricing
- Budget vs actual alerts via SMS
- Labor burden (taxes, insurance) auto-applied
- Profit by job type report
- QuickBooks class sync

## P2 Enhancements
- GPS time clock
- Purchase orders
- Vendor bills matching
- Change orders impact margin in real time

## Success Criteria for MVP
- 100% of won jobs have budget created
- 80% of jobs have at least 1 time entry
- Painters can tell you margin % within 5 seconds of opening job

---

Job costing is NOT a nice-to-have. It's the reason to switch.
