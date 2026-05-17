# Analytics & Observability

## PostHog Events

Funnel: lead_created → estimate_sent → estimate_viewed → estimate_accepted → invoice_sent → invoice_paid

Events:
- user_signed_up {org_id}
- lead_created {source}
- estimate_sent {value, packages_count}
- estimate_viewed {estimate_id}
- estimate_accepted {value}
- invoice_sent {value}
- invoice_paid {value, days_to_pay}
- job_closed {margin_pct}
- time_entry_created
- expense_created

All events include org_id, user_id, timestamp.

## Product Metrics Dashboard
- MRR, churn, LTV from Stripe
- Activation: % users sending first estimate <24h
- Close rate by lead source
- Avg job margin %

## Operational Metrics
- p95 API latency <300ms
- Error rate <0.5%
- Worker CPU time <10ms p95
- DB query time <50ms p95

## Logging
- Structured JSON with trace_id, org_id, user_id, route, duration
- Levels: error, warn, info, debug
- PII redacted

## Alerting
- Error rate >1% → Slack
- p95 latency >500ms → page
- Failed Stripe webhooks → Slack
- Neon storage >80% → email
