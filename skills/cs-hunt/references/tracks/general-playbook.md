# General Playbook

## Purpose

Use this step for meaningful issues that do not fit cleanly into injection, xss, auth, ssrf, or authz.

## Start Here

Start from the leftovers in `bootstrap/recon.md`'s `## Universal Attack Mapping`:

- attacker-controlled inputs that were never fully resolved by the directed domain steps
- trust boundaries that still look weak or assumption-heavy
- privileged effects that do not map neatly to one of the five directed lenses
- weird but plausible failure modes that deserve a concrete writeup

Also consume `bootstrap/recon.md`'s `## Static Pressure Points`:

- point issues that do not belong cleanly in another directed domain step
- dependency or secrets leads that deserve concrete operator treatment
- invariants and state transitions that should be pressure-tested

Also revisit `bootstrap/bootstrap.md`'s high-reward directions and likely pressure points, then review any unresolved or cross-cutting paths from `hunt/data-flow.md`.

## Business Logic Security Testing

This is the main home for business-logic security testing.

Infer the important invariants from code and product shape, then try to violate them with the cheapest credible proof path.

High-value invariant families:

- ownership and tenant isolation
- role or workflow approval boundaries
- state transitions such as draft -> published, pending -> approved, invited -> active
- billing, quota, discount, or credit application rules
- import/export and background-job assumptions
- support, admin, or recovery actions that should be tightly constrained

For each serious invariant, leave behind:

- `Invariant`: what the system appears to rely on
- `Where Enforced`: the code, helper, middleware, or state machine that should uphold it
- `Violation Attempt`: the concrete way an attacker or untrusted actor would try to break it
- `Cheapest Proof Path`: trace only, existing test, small harness, or localhost request
- `Verdict`: `safe`, `confirmed`, `strong-code-evidence`, or `needs-runtime-validation`

Do not invent fuzzers or giant harnesses just to satisfy the idea. Small, repo-specific violation attempts are enough.

## Look For

- business logic flaws
- information disclosure
- secrets or crypto issues
- dependency risk worth surfacing
- race or state issues
- repo-specific weirdness

## False-Positive Traps

- repeating a finding that already belongs cleanly in another step
- generic best-practice complaints with no concrete repo path
- dependency noise that does not change the operator's priorities
- vague "business logic issue" language without a clear invariant and violation path

## Output Shape

### `hunt/general.md`

Required sections:

1. `# General`
2. `## Domain Fit`
3. `## Recon And Data-Flow Leads Consumed`
4. `## Business Logic Invariants Tested`
5. `## Enumeration`
6. `## Reviewed Surfaces`
7. `## Findings`
8. `## Reviewed And Found Safe`
9. `## Hardening Notes`
10. `## Trust-Model Notes`
11. `## Validation Notes`
12. `## Why This Step Stayed Quiet` when there are no material findings

`## Findings` should distinguish:

- `confirmed`
- `strong-code-evidence`
- `needs-runtime-validation`

`## Recon And Data-Flow Leads Consumed` should note which static pressure points, universal-attack-map leftovers, or `hunt/data-flow.md` traces were actually carried into this step.

`## Business Logic Invariants Tested` should be concrete. For each serious invariant, include the invariant, where it is enforced, the attempted violation path, and the verdict.
