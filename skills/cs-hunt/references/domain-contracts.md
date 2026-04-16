# Hunt Domain Contract

Use the following checklist for each track.

## Shared Output Shape

`hunt/<track>/exploitation_queue.json` should use this shape:

```json
{
  "domain": "auth",
  "queue_length": 1,
  "vulnerabilities": [
    {
      "id": "AUTH-001",
      "domain": "auth",
      "title": "Missing server-side session rotation",
      "severity": "high",
      "state": "strong-code-evidence",
      "confidence": "high",
      "evidence": ["src/auth/session.ts:44"],
      "affected_routes": ["/login"],
      "summary": "Short statement of the flaw",
      "next_step": "What would prove it at runtime"
    }
  ]
}
```

`analysis.md` should explain the domain, reviewed surfaces, findings, and safe components.

Good `analysis.md` files should also explain:

- why the reviewed surfaces were chosen
- what would have made a candidate stronger or weaker
- which near-misses were reviewed and cleared

`evidence.md` should record:

- whether validation ran or was skipped
- what local proof was attempted
- what was confirmed
- what remains runtime-dependent

`status.json` should use this shape:

```json
{
  "domain": "auth",
  "analysis_complete": true,
  "queue_length": 1,
  "validation_ran": true,
  "confirmed_count": 0,
  "strong_code_evidence_count": 1,
  "needs_runtime_validation_count": 1
}
```

## Per-Domain Flow

For every domain:

1. Read `bootstrap/scope.md`, `bootstrap/pre_recon.md`, and `bootstrap/recon.md`.
2. Produce `analysis.md`.
3. Produce `exploitation_queue.json`.
4. If `queue_length > 0`, produce `evidence.md` from local validation.
5. If `queue_length == 0`, produce `evidence.md` with a skip record instead of fabricated testing.
6. Produce `status.json`.

All validation that can mutate state, start services, or create artifacts must run from the per-scan working copy under `reports/<repo>-<YYYY-MM-DD>/repo/`.

## Queue Quality Gate

Only place a candidate in `exploitation_queue.json` if:

- the path is concrete enough to hand off
- the affected route, job, parser, or helper is named
- the next proof step is actionable
- the candidate still looks meaningful after checking likely mitigations

Keep weak hunches in `analysis.md`, not in the queue.

## Evidence Expectations

`evidence.md` should prefer this structure:

1. what was tested
2. what local setup was used
3. what happened
4. what that means for confidence and state
5. what remains unproven, if anything

If validation is skipped, the skip reason should still teach the report writer something.

## Injection

Look for:

- raw SQL and query string assembly
- shell execution
- unsafe file path joins or traversal
- unsafe deserialization
- template execution on untrusted input

False-positive traps:

- parameterized query APIs that look scary in grep
- sanitizer calls that are contextual and actually sufficient
- tooling-only shell commands that are never hit from an app path

## XSS

Look for:

- server-rendered HTML sinks
- client-side HTML injection such as `innerHTML` or equivalent wrappers
- markdown and rich-text rendering
- user-controlled content that returns to a browser context
- weak sanitization or encode-for-the-wrong-context mistakes

False-positive traps:

- reflection into non-executable contexts
- escaped template output mistaken for raw HTML
- developer/test pages not served in the target runtime

## Auth

Look for:

- login and logout flows
- session creation, rotation, invalidation, and cookie flags
- JWT or token verification
- password reset, invite, magic-link, and MFA flows
- missing rate limits or account enumeration

False-positive traps:

- client-side password rules mistaken for server-side enforcement
- docs or config examples mistaken for active auth flows
- stale token helpers not used by the real login path

## Authz

Look for:

- route guards and policy checks
- role elevation paths
- ownership checks on object fetch and mutation
- tenant isolation boundaries
- admin or support-only functions exposed to lower roles

False-positive traps:

- route decorators without checking the underlying fetch/mutation logic
- admin code that is unreachable in the shipped runtime
- object IDs present in routes where the service layer still enforces ownership

## SSRF

Look for:

- outbound HTTP clients
- webhook dispatch and callback testing
- URL importers, image fetchers, feed parsers, and metadata fetchers
- storage proxies and signed URL generators
- internal hostname or protocol allowlist failures

False-positive traps:

- server-initiated requests with no attacker-controlled destination
- URL checks that look weak until post-normalization enforcement is traced
- timeout-only behavior without clearer proof of internal reachability

## Summary Output

`hunt/summary.md` should list:

- tracks completed
- queue lengths by domain
- confirmed findings by domain
- strong-code-evidence findings by domain
- runtime gaps
- highest-priority files for report synthesis

`hunt/concatenated_evidence.md` should concatenate the five `evidence.md` files in Shannon domain order:

1. injection
2. xss
3. auth
4. ssrf
5. authz
