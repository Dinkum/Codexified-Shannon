# Authz Playbook

## Look For

- missing ownership checks
- role checks only at the route layer
- tenant scoping gaps
- object-level access after an apparently safe lookup

## Ask

- what object, tenant, or role boundary is supposed to hold here?
- who decides that boundary?
- is ownership checked at every fetch and mutation, or only at some layers?

## False-Positive Traps

- route guards that look thin but are backed by service-layer authorization
- ownership checks implemented in a shared loader rather than inline
- admin-only flows clearly separated from normal user reachability

## Baseline Searches

- `authorize`
- `authorise`
- `policy`
- `can\\(`
- `permit`
- `role`
- `tenant`
- `owner`

## Output Shape

### `hunt/authz.md`

Required sections:

1. `# Authz`
2. `## Summary`
3. `## What We Looked Into`
4. `## Issues We Found And Verified`
5. `## General Concerns And Hardening Suggestions We Verified`
6. `## Misc Notes`

All sections should be flat lists.

Also maintain:

- `hunt/artifacts/authz/candidates_log.md`
- `hunt/artifacts/authz/verified_log.md`
