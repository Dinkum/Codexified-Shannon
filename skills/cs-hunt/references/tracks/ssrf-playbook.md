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
2. `## Summary`
3. `## What We Looked Into`
4. `## Issues We Found And Verified`
5. `## General Concerns And Hardening Suggestions We Verified`
6. `## Misc Notes`

All sections should be flat lists.

Also maintain:

- `hunt/artifacts/ssrf/candidates_log.md`
- `hunt/artifacts/ssrf/verified_log.md`
