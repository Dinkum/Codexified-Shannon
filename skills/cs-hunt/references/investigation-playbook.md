# Investigation Playbook

Use this when a domain review needs more depth than the main skill body provides.

## Shared Method

1. Start from a concrete surface:
   - route
   - job
   - webhook
   - parser
   - helper that gates access or builds a dangerous operation
2. Trace the path until you can answer:
   - what controls input or privilege here?
   - what transforms it?
   - what final sink or action matters?
   - what mitigation is supposed to stop abuse?
3. Try to break your own theory:
   - upstream validation
   - guard layering
   - framework defaults
   - type narrowing or allowlists
   - environmental constraints
4. Only then decide whether it belongs in:
   - `safe`
   - `strong-code-evidence`
   - `needs-runtime-validation`
   - `confirmed`

## Confidence Guidance

- `high`
  - the path is clear, the mitigation is absent or mismatched, and scope is known
- `medium`
  - the path is strong but one material assumption remains
- `low`
  - the idea is plausible but the path or scope is still blurry

When in doubt, round down.

## Domain-Specific Prompts

### Injection

Ask:

- does user input ever shape the structure of a query, shell command, file path, template, or deserializer input?
- are guards contextual, or just generic cleaning?
- does a later concat undo an earlier safety measure?

### XSS

Ask:

- where does attacker-controlled content return to a browser context?
- what is the exact render context?
- is the encoding matched to that context, or merely present?

### Auth

Ask:

- where is identity established?
- where are sessions or tokens created, rotated, and revoked?
- which defenses are real server-side controls versus UI suggestions?

### Authz

Ask:

- what object, tenant, or role boundary is supposed to hold here?
- who decides that boundary?
- is ownership checked at every fetch and mutation, or only at some layers?

### SSRF

Ask:

- can user input influence a server-side destination?
- what canonicalization, scheme, host, and IP checks exist?
- are allowlists real, contextual, and enforced after normalization?

## Good Runtime Moves

- hit the narrowest request path possible
- isolate one parser or helper with a tiny harness
- reuse existing tests where practical
- prefer localhost services over broad environment recreation

## Bad Runtime Moves

- booting the entire stack when one helper test would do
- treating a startup failure as proof a finding is false
- writing invasive patches when a trace or targeted repro is enough

