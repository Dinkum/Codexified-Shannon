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
2. `## Summary`
3. `## What We Looked Into`
4. `## Issues We Found And Verified`
5. `## General Concerns And Hardening Suggestions We Verified`
6. `## Misc Notes`

All sections should be flat lists.

Also maintain:

- `hunt/artifacts/auth/candidates_log.md`
- `hunt/artifacts/auth/verified_log.md`
- during exploration, append each candidate to `candidates_log.md` immediately when it is discovered
- once exploration for the step is complete, begin verification and append each verification outcome to `verified_log.md` immediately before checking the next candidate
