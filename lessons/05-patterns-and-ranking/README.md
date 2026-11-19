# 05 — Patterns and deterministic ranking

**Outcomes:** ÕV1, ÕV2, ÕV8  
**Time assumption:** 2 hours in class + approximately 2 hours independent work.

[Course overview](../../README.md) · [Project rules and milestones](../../PROJECT.md) · [Assessment](../../CAPSTONE.md)

## Before this session

Persistent mutations and a repository/service boundary work.

## Today’s goal

A filtered ranking pipeline with popular/newest strategies and vote-share calculations.

**Material status:** teaching plan. A detailed, version-checked Next.js walkthrough will be added when this session is prepared. The sequence below defines the intended work and acceptance checks.

## Concepts

### Strategy

Choose between named popular and newest comparison functions. Plain functions can implement Strategy; an inheritance hierarchy is not required.

### Repository and Adapter

The service depends on operations it needs, not Prisma details. A Prisma-backed implementation adapts that contract to the ORM. Pass it explicitly so another implementation can be used in a test.

### Ranking pipeline

Filter by genre, calculate the eligible vote total, sort using explicit tie rules, then derive shares. Return new results without mutating input. Rules and examples are in PROJECT.md.

### Complexity and limits

Filtering and aggregation are linear in the number of movies. General comparison sorting is O(n log n). Rounded percentages can total slightly above or below 100%. Discuss Observer as a comparison, without adding an event system solely to name another pattern.

## In-class sequence

| Minutes | Work |
|---|---|
| 0–20 | Calculate sample results by hand and agree the boundary table. |
| 20–55 | Implement pure strategies, filtering, and vote shares. |
| 55–80 | Connect the ranking choice and genre filter to the UI. |
| 80–100 | Review repository adaptation and explicit dependency passing. |
| 100–120 | Check empty input, ties, zero votes, and non-mutation; commit. |

## Independent work

Complete these required tasks before the next session:

1. Write docs/algorithm.md with pipeline order, examples, boundary table, and complexity.
2. Write docs/patterns.md pointing to each pattern and explaining when it helps.
3. Calculate expected results for ties, all-zero votes, a single movie, and a genre with no matches.

**Optional extension:** Add a third ranking strategy with documented tie rules; keep filtering and share calculations unchanged.

## Self-check

1. Why must the last tie-breaker be explicit?
2. Which denominator applies after filtering?
3. What does Repository hide, and what does it not hide?

## Exit checklist

- [ ] Both strategies match the documented rules.
- [ ] Filter scope and displayed shares agree.
- [ ] Input arrays are not mutated.
- [ ] Empty and zero-vote lists produce valid results.
- [ ] Explain the changed files and commit the completed work with a clear English message.
