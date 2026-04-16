# Data-Flow Playbook

## Purpose

Use this step to map attacker-controlled sources to sensitive sinks or protected actions before the later domain steps begin.

This is not a vulnerability class by itself. It is a routing and reasoning step that helps the later domain steps focus on the strongest real paths.

## Look For

- request, file, webhook, queue, CLI, env, or import-driven inputs
- transforms such as parsing, normalization, validation, encoding, decoding, or helper chaining
- guards or sanitizers that may or may not be sufficient in context
- sensitive sinks such as SQL queries, command execution, filesystem writes, template rendering, outbound fetches, deserialization, and privileged state changes
- protected actions such as ownership checks, approval gates, role transitions, billing actions, or other high-impact state changes

## Ask

- where does attacker influence enter?
- what meaningful transforms happen before the sink or protected action?
- what guard or sanitizer is supposed to make this safe?
- is that guard actually sufficient for this exact sink and context?
- which later step should own the follow-up?

## False-Positive Traps

- treating any source and any sink in the same file as a real path
- trusting a sanitizer by name without checking what it actually does
- assuming a transform preserves safety when a later concat or re-parse undoes it
- routing a path to multiple later steps when one is clearly primary

## Baseline Searches

- `req\\.`
- `request\\.`
- `params`
- `query`
- `body`
- `headers`
- `cookies`
- `process\\.env`
- `os\\.environ`
- `exec\\(`
- `execSync\\(`
- `spawn\\(`
- `\\.query\\(`
- `\\.raw\\(`
- `fetch\\(`
- `axios`
- `httpx`
- `requests\\.`
- `render`
- `dangerouslySetInnerHTML`
- `yaml\\.load\\(`
- `pickle\\.loads`

## Output Shape

### `hunt/data-flow.md`

Required sections:

1. `# Data Flow`
2. `## Summary`
3. `## What We Looked Into`
4. `## Issues We Found And Verified`
5. `## General Concerns And Hardening Suggestions We Verified`
6. `## Misc Notes`

All sections should be flat lists.

In this step:

- `## What We Looked Into` should cover the main source families, sink families, and protected actions reviewed
- `## Issues We Found And Verified` should include routing-worthy paths that survived review
- `## General Concerns And Hardening Suggestions We Verified` should include verified guard or sanitization weaknesses and the hardening suggestions they point to, even when they are not yet reportable issues

Also maintain:

- `hunt/artifacts/data-flow/candidates_log.md`
- `hunt/artifacts/data-flow/verified_log.md`
- during exploration, append each candidate to `candidates_log.md` immediately when it is discovered
- once exploration for the step is complete, begin verification and append each verification outcome to `verified_log.md` immediately before checking the next candidate
