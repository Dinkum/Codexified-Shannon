# CS Report Output Shape

## Final Output

### `report.md`

Use the following as the canonical report contract to structure your final report.

# Security Assessment Report

## Executive Summary
A short narrative summary. Explain what kind of app this is, what was scanned, what the headline finding is, and how worried the operator should be. Write this for a decision-maker: minimal jargon, no per-track enumeration. End with the single sentence the operator should remember if they read nothing else.

## Prioritized Findings
All runtime or code confirmed `P0` and `P1` , pulled across all steps. For each, give the title, source step, a one-line reason it cannot wait, and a fix direction.

If none exist, say so plainly and name the strongest reviewed control as context. An empty section here is a real result.

## Context (Bootstrap/Meta)

### What We Scanned
The repo shape, runtime units, and surfaces actually touched. Two paragraphs is plenty.

### What We Could Not Check
Coverage gaps: subdomains skipped, runtime posture limits, tools the operator did not approve, or validation paths that could not be exercised. Be specific: `Secret scanning fell back to grep — gitleaks declined` is better than `limited coverage`.

## Hunt

### Summary
A short technical summary of the hunt phase. Call out any cross-step chains, themes, or shared-root issues worth flagging at the summary level. Prose/narrative overview NOT a per track enumeration.

Keep this brief. The detail belongs in `### Findings By Track`.

### Findings By Track
One subsection per hunt step in scan order.

### <Track Name>

#### Summary
Open with a brief overall narrative only if there is one worth giving. If the step stayed quiet, say that here and move on. Do not pad.

#### Finding
Only include this when findings exist.

Use this block for any material item from the step's `## Issues We Found And Verified` section. A finding does not need to be runtime-confirmed to belong here.
Do not bury a concrete issue in `#### Summary` just because it is only `code-supported`.

For each finding:
- **Title** and severity
- **Proof state** — `runtime-confirmed` or `code-supported`
- **What it is** — the issue, with specific file, route, or function references, and how it was established
- **If exploited** — what it costs the user, system, or data
- **Suggested fix** — concrete direction tied to a specific code layer

Order findings within a step by severity.

## Hardening
Forward-looking suggestions to make the app more resilient. These are not findings — nothing is broken today — but they would meaningfully reduce risk going forward. Only include low-risk, high-reward improvements, sorted by priority.

Bias toward specific, high-yield improvements over generic best-practice nags. Give each hardening item its own small subsection under `## Hardening`. For each:
- `### <Title>`
- **What to do** — the change, concretely
- **Where it lives** — file, module, or surface area
- **Why it matters** — what attack class or future regression it prevents

## Reviewed And Found Safe
The tempting surfaces that held up under review. For each:
- What was reviewed
- What control held
- Where a future regression would be dangerous

Keep this honest. Only include surfaces a reasonable reviewer would have actually worried about. This is coverage proof, not a victory lap.

## Misc Notes
Optional for things that you would like to say that do not neatly fit in other sections.

## What's Next
A short handoff. The 1-3 things to do next, the biggest unresolved uncertainty, and any direction worth pursuing in a more rigorous follow-up.

## Implementation Notes

- Before drafting the report, do one quick pass through all bootstrap and hunt markdown artifacts so the narrative is based on the full scan rather than only the aggregate files.
- Bootstrap context comes from `bootstrap/scope.md`, `bootstrap/inventory.md`, `bootstrap/recon.md`, and `bootstrap/bootstrap.md`.
- Hunt context comes from `hunt/data-flow.md`, the seven later hunt step files, and `hunt/hunt.md`.
- Raw support comes from `hunt/artifacts/` or top-level `artifacts/` only when it materially strengthens a claim.
- Per-step findings come from `## Issues We Found And Verified`.
- Every item in a step's `## Issues We Found And Verified` must appear in the final report. Do not omit it, collapse it into summary prose, or merge it into another finding. Each issue gets its own separate `#### Finding` block under that step.
- Per-step hardening comes from `## General Concerns And Hardening Suggestions We Verified`.
- Reviewed-safe surfaces come from safe calls in each step's `verified_log.md` plus clearly safe bullets in `## What We Looked Into`.
- `## Hunt > ### Summary` should be a technically informed narrative/prose summary of the hunt phase, not a per-step enumeration.
- Use it to call out the biggest themes, cross-step chains, shared-root issues, and overall hunt shape.
- `## Hardening` should be built from bootstrap hardening leads, per-step hardening notes, and low-risk, high-reward engineering or resilience suggestions.
- `## Misc Notes` is optional and should usually be absent.
