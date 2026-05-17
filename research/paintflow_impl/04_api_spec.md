# API Spec v1

Base: `https://api.paintflow.app/v1`
Auth: Cookie session, org_id from session
Headers: `Idempotency-Key` for POST

## Leads
POST /leads {name, phone, email, source} → 201
GET /leads?status=new → 200 [{...}]
PATCH /leads/:id {status}

## Estimates
POST /estimates {lead_id, packages} → 201
POST /estimates/:id/send → 202 (enqueues SMS/email)
GET /estimates/:id/public → 200 (public)
POST /estimates/:id/accept {signature} → 200

## Messages
POST /messages {lead_id, body} → 201 (sends SMS)
GET /messages?lead_id= → 200

## Jobs
POST /jobs {estimate_id, scheduled_at} → 201
GET /jobs?week=2026-05-19 → 200

## Time Entries
POST /time-entries {job_id, hours, task, date} → 201

## Expenses
POST /expenses (multipart: amount, category, receipt) → 201 {receipt_url}

## Costing
GET /jobs/:id/costing → 200 {budget, actual, variance, margin_pct}

## Invoices
POST /invoices {job_id, total} → 201 {stripe_checkout_url}
POST /webhooks/stripe → 200

## Errors
{error: {code, message, details}}
Codes: UNAUTHORIZED, FORBIDDEN, NOT_FOUND, VALIDATION_ERROR, RATE_LIMITED

## Pagination
Cursor-based: `?cursor=...&limit=50`
Response: {data:[], next_cursor}
