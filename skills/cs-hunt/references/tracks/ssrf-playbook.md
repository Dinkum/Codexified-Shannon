# SSRF Playbook

## Look For

- user-controlled outbound destinations
- weak host, scheme, or IP validation
- callback, webhook, proxy, or image-fetch flows

## Ask

- can user input influence a server-side destination?
- what canonicalization, scheme, host, and IP checks exist?
- are allowlists real, contextual, and enforced after normalization?

## False-Positive Traps

- fixed outbound destinations mislabeled as attacker-controlled
- hostname checks that are strong enough after normalization
- outbound calls reachable only from trusted operator-only paths that are clearly out of scope

## Baseline Searches

- `fetch(`
- `axios`
- `requests\\.`
- `httpx`
- `urllib`
- `aiohttp`
- `webhook`
- `callback`
- `proxy`

## Output Shape

### `hunt/ssrf.md`

Required sections:

1. `# SSRF`
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
