# ADRs

## ADR-001: Cloudflare-first Architecture
**Date:** 2026-05-17
**Status:** Accepted

**Context:** Need global edge performance for mobile painters, low cost.

**Decision:** Use Cloudflare Pages + Workers + R2 + Hyperdrive.

**Consequences:**
+ Sub-100ms latency worldwide
+ No servers to manage
+ Integrated security
- Workers CPU limits
- Vendor lock-in

## ADR-002: Drizzle over Prisma
**Date:** 2026-05-17
**Status:** Accepted

**Context:** Prisma requires Node runtime, not ideal for Workers.

**Decision:** Drizzle ORM with Neon HTTP driver.

**Consequences:**
+ Native Workers support
+ Smaller bundle
- Less mature ecosystem

## ADR-003: RLS for Multi-tenancy
**Date:** 2026-05-17
**Status:** Accepted

**Context:** Need strong tenant isolation.

**Decision:** Postgres RLS with org_id, middleware sets session var.

**Consequences:**
+ Database-enforced isolation
+ Requires careful middleware
+ Needs automated isolation tests

## ADR-004: Follow-ups as P0
**Date:** 2026-05-17
**Status:** Accepted

**Context:** PaintTalk validation: 30% leads lost to no follow-up.

**Decision:** Move drip automation to MVP weeks 5-6.

**Consequences:**
+ Higher close rates
+ More engineering complexity early

## ADR-005: Cloudflare Workers Long-term Strategy
**Date:** 2026-05-17
**Status:** Accepted

**Context:** Need to decide if Workers scales beyond few tenants or if full servers needed.

**Decision:** Commit to Cloudflare Workers for MVP and first 2 years up to ~5,000 orgs. Build with abstraction layer for future hybrid migration.

**Phased Approach:**
- Phase 1 (0-1k orgs): Pure Workers + Queues + Hyperdrive
- Phase 2 (1k-10k orgs): Hybrid - Edge API for reads, Fly.io for heavy writes/jobs
- Phase 3 (10k+ orgs): Re-evaluate based on metrics

**Consequences:**
+ Edge performance and low ops overhead now
+ Pay-per-request cost model fits bursty usage
+ Escape hatches built in from day one
- Need to monitor CPU p95 monthly
- Some workloads (PDF gen, bulk sync) offloaded to Queues/Browser Rendering
- Vendor lock-in mitigated by abstraction layer

**Monitoring Triggers for Migration:**
- Worker CPU p95 >20s for 2 consecutive months
- Consistent queue backlog
- Need for persistent WebSockets

**Mitigations:**
- Keep business logic in `/packages/core` runtime-agnostic
- Use standard Web APIs where possible
- Queue consumers can be moved to Fly later without API changes
