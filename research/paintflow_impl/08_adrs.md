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
