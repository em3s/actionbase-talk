# Did We Need a Dedicated Interaction Database — or Did We Overbuild?

- Source: https://github.com/kakao/actionbase/discussions/32
- Created: 2026-01-12

> 외부 자료 보존 사본. 원문이 사라질 수 있어 verbatim으로 보관.

---

## Original Post

Actionbase grew out of concrete needs in our internal environment.

The same interaction features—likes, views, follows—were being rebuilt across teams, each time with different stacks, different schemas, different failure modes. And when traffic grew, each hit similar scaling walls.

Actionbase emerged as an attempt to stop solving the same problem ten different ways—and to solve it at scale.

## What is Actionbase?

Actionbase is a database built to serve user interactions in real time, currently on HBase.

| Focuses on | Explicitly avoids |
|------------|-------------------|
| Real-time user interactions (likes, views, follows) | General-purpose graph queries |
| Bounded access patterns (GET, COUNT, SCAN) | Unbounded traversal or analytics |
| Continuous writes, immediate reads | Batch ingestion or deferred indexing |
| WAL/CDC to Kafka (yours or ours) | Owning downstream processing |
| Pluggable storage (HBase now, others planned) | Building yet another storage engine |

An interaction is modeled as: **who** did **what** to which **target**.

Actionbase focuses on a small set of high-frequency operations:
- Edge lookups (GET, including multi-get)
- Edge counts (COUNT)
- Indexed edge scans (SCAN)

Typical use cases include per-item interaction states, interaction counts in feeds, per-user interaction histories, and ordered lists of interacted items.

Despite appearing as different product features, Actionbase treats all of these uniformly as interactions—with what that requires: accurate counts, consistent toggles, and ordering despite out-of-order events, at scale.

## An Open Question

If you've built or operated interaction-heavy, user-facing systems, we want to learn from your experience.

1. **If you've seen the same features rebuilt across teams:**
   - What made it painful—diverging schemas, duplicated effort, inconsistent behavior?
   - Did you try to consolidate? What worked, what didn't?
2. **If you've scaled a single system (e.g., likes) until it hit a wall:**
   - What broke first—sharding, cache consistency, counting?
   - What did you choose next—and did it hold?
3. **If you know of existing solutions:**
   - Are there systems—open source, commercial, or publicly shared—that solve this well?

We took the consolidation path—and scaling came with it. Curious whether others faced the same fork, or found a different way entirely.

## Why We're Asking

Actionbase runs in production at Kakao, handling over a million requests per minute. But proving something works is not the same as proving it was the right call. What you see here is what survived—through hitting walls, live incidents with users waiting, and fixes shipped while complaints were still coming in.

If there's more we missed—a path we should have taken, or a failure mode waiting to happen—that's exactly what we're here to find.

---

## Comments

### 2026-01-14

As the author: I don't consider Actionbase a default choice. If a single database instance can handle the workload, that's almost always the better answer.

Actionbase exists not because relational models failed, but because organizational realities accumulated faster than schemas could.

### 2026-01-18

Writing this—and rewriting it—made us reflect on something we hadn't fully recognized.

Consolidation solved fragmentation, but created a different risk. Before, the bus factor was spread across teams. Now it's concentrated in one team—and a small one.

Perhaps this was part of why we open-sourced, even if we didn't realize it at the time.
