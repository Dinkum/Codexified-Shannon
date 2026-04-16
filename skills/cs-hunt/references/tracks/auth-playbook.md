# Auth Playbook

## Look For

- weak identity establishment
- broken session lifecycle
- token issuance, storage, or revocation gaps
- flows that trust UI state more than server-side controls

## Ask

- where is identity established?
- where are sessions or tokens created, rotated, and revoked?
- which defenses are real server-side controls versus UI suggestions?

## False-Positive Traps

- frontend-only auth affordances treated as true controls
- safe framework defaults mistaken for broken custom auth
- token helpers that look weak in isolation but are wrapped by a stronger server-side flow

## Baseline Searches

- `login`
- `session`
- `token`
- `jwt`
- `refresh`
- `password reset`
- `rotate`
- `revoke`

## Output Shape

### `hunt/auth.md`

Required sections:

1. `# Auth`
2. `## Domain Fit`
3. `## Enumeration`
4. `## Reviewed Surfaces`
5. `## Findings`
6. `## Reviewed And Found Safe`
7. `## Hardening Notes`
8. `## Trust-Model Notes`
9. `## Validation Notes`
10. `## Why This Step Stayed Quiet` when there are no material findings

`## Findings` should distinguish:

- `confirmed`
- `strong-code-evidence`
- `needs-runtime-validation`
