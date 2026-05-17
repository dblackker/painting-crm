# Architecture

## Stack
- Frontend: Astro 5 + React, Tailwind, shadcn, PWA
- API: Cloudflare Workers + Hono
- DB: Neon Postgres + Drizzle ORM + Hyperdrive
- Auth: Better Auth, Workers KV sessions
- Storage: Cloudflare R2
- Payments: Stripe
- SMS/Email: Twilio + Resend
- Analytics: PostHog
- Hosting: Cloudflare Pages + Workers

## Multi-Tenancy
- org_id on all tables
- RLS policies enforce tenant isolation
- Middleware sets `app.current_org_id` per request
- Automated CI tests verify cross-org access fails

## System Diagram
User → CDN → Pages → Workers API → Neon (via Hyperdrive) → R2/Stripe/Twilio

Queues handle drips, PDF gen, webhooks.

## Directory Structure
```
/apps/web
/apps/api
/packages/db
/packages/ui
/infra
AGENTS.md
CLAUDE.md per app
```

## Key Decisions
- Drizzle over Prisma for Workers compatibility
- Workers KV for sessions, not D1
- Hyperdrive for connection pooling
- Queues for async work
