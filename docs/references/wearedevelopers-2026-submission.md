# I Built a Database Nobody Asked For

- Source: WeAreDevelopers World Congress 2026 — session submission

> 외부 자료 보존 사본. 제출 내용만 보관 (상태·개인 정보 제외).

---

## Description

I built a database nobody asked for. Here's why.

Likes, follows, recent views. They start simple—a single table, some indexes, a cache. When COUNT gets slow, add another cache. For a while, this is enough.

But at scale, something changes. User interactions become write-in-the-request-path workloads. Every click mutates state, must eventually succeed, and is immediately queried by user-facing features.

At Kakao, these features were built repeatedly across teams—different schemas, caches, failure modes. None wrong individually, but cumulative cost kept growing.

It started with a realization: these were all edges in a graph, sharing the same data model. By solving concrete needs, we converged on a single database.

The result: Actionbase—precomputes at write time. In production since 2024, millions of requests per minute at peak. But that left uncomfortable questions: did we really need this—or did we overbuild? What haven't we seen yet?

This is a story of building when no one believed it would work—and proving it did.

## What I'll cover

- When an interaction table stops being "just another table"—the signals: sharding limits, cache inconsistency, same bugs across teams
- Why write-in-the-request-path workloads break caching and async patterns
- The hard part: write-time materialization (edges, counts, indexes, event ordering) without a reference implementation
- Surviving the pressure: production incidents, no one to help, fixes before trust collapsed
- The choice I'd make again: start from the bottom, solve concrete problems, stay close to service teams

## 30 min breakdown

- 0–5m: The moment "just a table" stops working
- 5–10m: What everyone tried (cache, async, sharding)—and why it kept failing
- 10–20m: A different path—building Actionbase
- 20–25m: Evolving under pressure—incidents, trust, recovery
- 25–30m: Q&A

## Metadata

- **Category:** Software Architecture & System Design
- **Session format:** Session (30 min, incl. Q&A)
- **Tags & Keywords:** Databases, Distributed Systems, System Design
- **Audience Level:** Intermediate

## Target Audience

Backend engineers, system architects, and tech leads building user-facing systems with interactions such as likes, follows, feeds, counters, or activity histories.

## Key Learnings

- How to recognize when interaction data stops being "just another table"
- Signals that caching and async patterns are breaking—before they break production
- The uncomfortable question: did we really need this—or could we have stayed simple?
- The choice I'd make again: when to build, when to leave it alone

## Links to previous talks, presentations or projects

- GitHub (Open Source): https://github.com/kakao/actionbase
- Production Stories: https://actionbase.io/stories/
- Previous Talk (Korean): https://www.youtube.com/watch?v=8-hVAFVHISE
- Documentation: https://actionbase.io/
