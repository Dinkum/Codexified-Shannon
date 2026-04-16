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
