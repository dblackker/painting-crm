# Data Model

## Core Tables

organizations(id, slug, name, plan, stripe_customer_id, created_at)
users(id, email, name, created_at)
memberships(user_id, org_id, role)
leads(id, org_id, name, phone, email, status, source, created_at)
estimates(id, org_id, lead_id, packages_json, total, status, sent_at, accepted_at)
jobs(id, org_id, estimate_id, scheduled_at, status)
invoices(id, org_id, job_id, stripe_payment_intent_id, total, status)
job_costs(id, org_id, job_id, budget_json, actual_json)
time_entries(id, org_id, job_id, user_id, hours, task, date)
expenses(id, org_id, job_id, amount, category, receipt_url, vendor)
messages(id, org_id, lead_id, direction, body, twilio_sid, created_at)
drip_enrollments(id, org_id, estimate_id, step, scheduled_at, sent_at)
audit_logs(id, org_id, user_id, action, entity_type, entity_id, ip, created_at)

## Indexes
- All tables index org_id
- leads(status, created_at)
- estimates(status)
- jobs(scheduled_at)

## RLS Example
```sql
ALTER TABLE leads ENABLE ROW LEVEL SECURITY;
CREATE POLICY org_isolation ON leads
  USING (org_id = current_setting('app.current_org_id')::uuid);
```

## JSON Schemas
packages_json: [{name, items:[{desc, qty, rate}], total}]
budget_json: {labor_hours, labor_cost, materials, subs, overhead}
