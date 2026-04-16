# Optimization Playbook

## Purpose

Use this final step to surface `LOW RISK HIGH REWARD` wins: improvements that save time, reduce duplication, or improve scaling without needing risky rewrites.

This is not a vulnerability step. It is an operator-facing optimization pass for obvious wins that are cheap to understand and cheap to implement.

## Look For

- avoidable O(n) or worse scaling on hot paths
- repeated parsing, serialization, hashing, rendering, or network calls
- duplicate work across layers or helpers
- over-engineered abstractions that add real runtime cost or complexity
- repeated DB queries or unnecessary fan-out
- caching opportunities with low correctness risk
- tight loops, large scans, or repeated allocations that obviously do not need to happen

## Ask

- is this path likely to run often enough that the cost matters?
- is the waste structural and obvious, or just theoretical?
- can the improvement be made without changing product behavior or architecture?
- is there a simpler abstraction or reuse point that would save time without creating new risk?
- would a strong engineer likely accept this as a low-risk cleanup rather than a rewrite?

## False-Positive Traps

- micro-optimizing cold code
- recommending a rewrite instead of a practical win
- performance advice with no concrete file or path
- caching suggestions that introduce correctness or invalidation risk
- style complaints dressed up as performance issues

## Baseline Searches

- `for `
- `forEach`
- `map\\(`
- `filter\\(`
- `reduce\\(`
- `find\\(`
- `includes\\(`
- `JSON\\.parse`
- `JSON\\.stringify`
- `SELECT`
- `query\\(`
- `fetch\\(`
- `axios`
- `requests\\.`
- `cache`
- `memo`

## Output Shape

### `hunt/optimization.md`

Required sections:

1. `# Optimization`
2. `## Summary`
3. `## What We Looked Into`
4. `## Issues We Found And Verified`
5. `## General Concerns And Hardening Suggestions We Verified`
6. `## Misc Notes`

All sections should be flat lists.

In this step:

- verified `LOW RISK HIGH REWARD` wins may land in either section
- use `## Issues We Found And Verified` when the win is specific and bounded
- use `## General Concerns And Hardening Suggestions We Verified` when the win is broader or cross-cutting

Also maintain:

- `hunt/artifacts/optimization/candidates_log.md`
- `hunt/artifacts/optimization/verified_log.md`
- during exploration, append each candidate to `candidates_log.md` immediately when it is discovered
- once exploration for the step is complete, begin verification and append each verification outcome to `verified_log.md` immediately before checking the next candidate
