---
name: cs-report
description: Synthesize the local security scan artifacts into one final markdown report at report.md. Use after cs-bootstrap and cs-hunt when the scan needs to be turned into an operator-facing report.
---

# CS Report

## Overview

`cs-report` assembles the final report from the bootstrap and hunt artifacts. It should stay disciplined and explicit about the local-only validation model.

The report should read like a serious operator handoff: decisive where proof exists, candid where it does not, and useful enough that a strong engineer or security lead could act on it immediately.

Use `references/cs-report-output-shape.md` as the required report structure.

## Inputs

- Target repository path
- Active scan output root: `reports/<repo>-<YYYY-MM-DD-HH-MM>/`
- Bootstrap artifacts:
  - `bootstrap/scope.md`
  - `bootstrap/inventory.md`
  - `bootstrap/recon.md`
  - `bootstrap/bootstrap.md`
  - `bootstrap/handoff.json`
- Hunt artifacts:
  - `hunt/data-flow.md`
  - `hunt/injection.md`
  - `hunt/xss.md`
  - `hunt/auth.md`
  - `hunt/ssrf.md`
  - `hunt/authz.md`
  - `hunt/general.md`
  - `hunt/optimization.md`
  - `hunt/hunt.md`
- Raw support from `hunt/artifacts/` or top-level `artifacts/` when it materially helps

## Required Output

- `report.md`

## Reporting Rules

1. Read `references/cs-report-output-shape.md` before writing.
2. Treat the wording in `references/cs-report-output-shape.md` as the canonical report contract. Preserve it closely rather than paraphrasing it.
3. Do one initial pass through all bootstrap and hunt markdown artifacts before drafting anything:
   - `bootstrap/scope.md`
   - `bootstrap/inventory.md`
   - `bootstrap/recon.md`
   - `bootstrap/bootstrap.md`
   - `hunt/data-flow.md`
   - `hunt/injection.md`
   - `hunt/xss.md`
   - `hunt/auth.md`
   - `hunt/ssrf.md`
   - `hunt/authz.md`
   - `hunt/general.md`
   - `hunt/optimization.md`
   - `hunt/hunt.md`
4. Use bootstrap for scope, architecture, trust-boundary, and runtime context.
5. Use `hunt/data-flow.md`, the seven later hunt step files, and `hunt/hunt.md` for the security narrative.
6. In `## Findings By Track`, include any material item carried by a hunt step's `## Issues We Found And Verified` section, not only runtime-confirmed items.
   - `runtime-confirmed` items stay findings
   - material `code-supported` items also stay findings
   - do not bury a concrete issue in `#### Summary` just because runtime proof was incomplete
7. `## Prioritized Findings` lists runtime-confirmed `P0` and `P1` only.
8. `## Hardening` is built from bootstrap hardening leads plus each step's `## General Concerns We Found And Verified`.
9. `## Misc Notes` is optional and should appear only when something useful does not fit elsewhere.
10. Cite raw artifacts only when they materially strengthen a claim.
11. Keep the output local-only.

## Synthesis Tips

- Normalize severity and confidence across hunt steps before writing the main report.
- Pull the strongest code references up into the narrative.
- Make quiet or inapplicable hunt steps explicit so the report feels deliberate.
- When findings are absent, still surface the most important reviewed controls and the biggest remaining uncertainty.
- Build `### What We Scanned` from `bootstrap/inventory.md`, especially its executive summary, application shape, and service map.
- Build `### What We Could Not Check` from `bootstrap/scope.md`'s blocking assumptions, runtime posture limits, declined tooling, and unresolved validation gaps surfaced during hunt.
- Keep proof state separate from report class:
  - proof state says how strongly something was proven
  - report class says what kind of note it is
- For each material finding, make the proof chain visible:
  - what breaks
  - where it breaks
  - how the evidence establishes it
  - why it matters in practice
- Reserve `#### Summary` for step-level narrative. If a step surfaced a concrete issue with operator action attached, promote it into `#### Finding`.
- Build per-step findings from `## Issues We Found And Verified`, not from stray narrative in `## Summary` or `## Misc Notes`.
- Build per-step hardening from `## General Concerns We Found And Verified`.
- Build `## Reviewed And Found Safe` from safe calls captured in each step's `verified_log.md` plus clearly safe bullets in `## What We Looked Into`.
- Keep `## Prioritized Findings` short. It is a triage list, not a second copy of the full report.
- In `## Hardening`, pull forward the highest-value notes from bootstrap and hunt, then explain what to do, where it lives, and why it matters.
- In `## Reviewed And Found Safe`, show which tempting surfaces were reviewed and what control held up.
- Build `## What's Next` from `hunt/hunt.md`'s unresolved paths and suggested next moves.
- End `## What's Next` with a short, concrete handoff.
- Use `## Misc Notes` sparingly. If it starts carrying real findings or coverage information, it belongs somewhere else.
