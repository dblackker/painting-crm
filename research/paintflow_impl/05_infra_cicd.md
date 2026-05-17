# Infra & CI/CD

## Environments
- prod: paintflow.app
- preview: pr-#.paintflow.pages.dev + Neon branch
- dev: local

## GitHub Actions Pipeline

Jobs:
1. lint-typecheck
2. unit-test
3. create-neon-branch
4. drizzle-migrate
5. deploy-preview (Workers + Pages)
6. e2e-playwright
7. On merge to main:
   - deploy prod Workers
   - deploy prod Pages
   - run migrations
   - smoke tests
   - notify Slack

## Secrets Management
- GitHub OIDC → Cloudflare API token (short-lived)
- Wrangler secrets set via CI
- Stripe/Twilio keys in Cloudflare secrets

## Database Migrations
- Drizzle migrations in /packages/db/migrations
- `drizzle-kit migrate` runs in CI before deploy
- Rollback: `drizzle-kit rollback`

## Preview Environments
- PR opens → create Neon branch → run migrations → deploy preview
- PR close → delete branch

## Observability
- Logs: Pino JSON → Cloudflare Logs → Axiom
- Tracing: trace_id in headers, propagated
- Sentry for errors, source maps uploaded in CI
- Uptime checks: Better Uptime

## Backups
- Neon PITR daily, 7-day retention
- R2 versioning enabled
- Quarterly restore drill

## Security
- Rate limit: 100 req/min/org via Cloudflare Rate Limiting
- Turnstile on signup
- R2 signed URLs 15min expiry
- Audit logs for all mutations
