# Recon Playbook

Use this when the repo is large, multi-service, or initially confusing. Recon should turn the inventory into a repo-specific attack map and runtime handoff.

## Questions To Answer Early

- What actually starts in production or local dev?
- What should not be started yet because it looks destructive, expensive, or irrelevant?
- Which package or directory owns the main request path?
- Where do auth, authz, persistence, and external calls live?
- What are the real attacker-controlled inputs, trust boundaries, and privileged actions?
- Which stack-aware best-practice refs actually match this repo, and what defaults or footguns do they imply?
- What code is custom enough that framework defaults no longer protect it?
- Which localhost targets would be most useful later if runtime validation becomes necessary?

## Universal Attack Mapping

Before writing recon, build a short universal attack map:

- Every real vulnerability is attacker-controlled influence crossing a boundary without the intended control holding.
- For every meaningful surface, ask:
  1. what can an attacker control
  2. where does that input first land
  3. what parses, normalizes, transforms, or routes it
  4. what trust boundary is supposed to hold
  5. what code enforces that boundary
  6. what privileged action or dangerous sink happens next
  7. what assumption is the code making
  8. what breaks if that assumption is false
  9. how you would prove or disprove this cheaply

Prioritize:

- external inputs
- trust boundaries
- privileged effects

Keep this concrete and repo-specific. The point is to sharpen hunt, not to produce a formal threat model.

## Static Pressure Sweep

Before hunt starts, do one lightweight static pass that sharpens what hunt should pressure-test:

- point issues such as weak crypto, hardcoded credentials, insecure config, disabled TLS verification, permissive CORS, weak randomness, or dangerous flags
- secrets clues and custom credential handling
- dependency hotspots worth later review
- business-logic invariants and state transitions that `general.md` should actively test
- hardening leads where controls are missing, fragmented, opt-in, inconsistent, or likely to regress

This is not a standalone static-analysis stage. Keep it concise and repo-specific.

## Best-Practice Lens

When the detected stack matches files under `references/security-best-practices/`, load only the relevant files.

- Use them to recognize likely safe defaults, likely framework-specific footguns, and places where custom code weakens the default posture.
- If both frontend and backend materially affect the attack surface, load both sides.
- Distill what matters into `Framework Security Expectations` and `Assumptions To Test`.

## Output Shape

### `bootstrap/recon.md`

Required sections:

1. `# Recon`
2. `## Attack Surface Summary`
3. `## Universal Attack Mapping`
4. `## Static Pressure Points`
5. `## Network-Reachable Entry Points`
6. `## Authorization And Trust Boundaries`
7. `## Framework Security Expectations`
8. `## Assumptions To Test`
9. `## Hardening Leads`
10. `## Runtime Handoff`
11. `## Open Questions`

Write this as an operator-ready attack map, not as loose notes.
