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
2. `## Domain Fit`
3. `## Sources Reviewed`
4. `## Sink And Protected-Action Families Reviewed`
5. `## Candidate Traces`
6. `## Guard And Sanitization Review`
7. `## Domain Routing`
8. `## Highest-Value Paths`
9. `## Hardening Notes`
10. `## Validation Notes`
11. `## Why This Step Stayed Quiet` when there are no material paths

`## Candidate Traces` should be concrete. For each serious path, include:

- `Source`
- `Transforms`
- `Guard Or Sanitizer`
- `Sink Or Protected Action`
- `Sufficiency Judgment`
- `Mapped Step`
- `Cheapest Proof Path`
- `Verdict`

Use `## Hardening Notes` for guard or sanitization improvements that would materially reduce risk even when the current path does not rise to a confirmed finding.
