# Report Contract

## Final Outputs

### `report/concatenated_findings.md`

This is the Shannon-style intermediate assembly artifact. It should be built by concatenating the five domain `evidence.md` files in Shannon order, then trimming duplicate headings or empty sections.

### `report.md`

Required sections:

1. `# Security Assessment Report`
2. `## Executive Summary`
3. `## Prioritized Findings`
4. `## Scope And Method`
5. `## Architecture And Attack Surface`
6. `## Confirmed Findings`
7. `## Strong Code Evidence Requiring Runtime Validation`
8. `## Reviewed And Found Safe`
9. `## Coverage Gaps`
10. `## Critical Files`
11. `## Appendix: Artifact Index`

Good reports should make the distinction between:

- confirmed findings
- strong code evidence
- runtime gaps
- inapplicable domains

## `## Prioritized Findings` Expectations

- Include only confirmed `P0` and `P1` findings.
- For each item, include:
  - finding title
  - priority (`P0` or `P1`)
  - why it is urgent in one short line
  - a brief remediation direction or best starting fix point
- If no confirmed `P0` or `P1` findings exist, state that explicitly instead of leaving the section empty.

### `report/executive-summary.md`

Keep this to a short, standalone summary:

- repo name
- scan date
- confirmed finding count
- highest-risk domains
- top remediation priorities

### `report/findings.json`

Consolidate all non-safe hunt findings into a single array. Preserve:

- `id`
- `domain`
- `title`
- `severity`
- `state`
- `confidence`
- `evidence`
- `affected_routes`
- `summary`
- `next_step`

## Synthesis Rules

- Bootstrap context comes from `bootstrap/scope.md`, `bootstrap/pre_recon.md`, and `bootstrap/recon.md`.
- Domain evidence comes from `hunt/concatenated_evidence.md` and the underlying per-domain files.
- Confirmed findings come first and should read like Shannon's exploit-backed report sections.
- Prioritized findings should be concise and operational, not miniature copies of the full finding writeups.
- Keep `strong-code-evidence` separate so the operator can decide whether to run a localhost validation pass later.
- Coverage gaps should describe what the local-only model could not prove.
- The artifact index should enumerate every generated file under the active scan directory that materially supports the report.
- When no findings are confirmed, the report should still explain the strongest reviewed controls and the main unresolved uncertainty.
