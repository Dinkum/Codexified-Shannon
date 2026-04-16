---
name: shannon-report
description: Synthesize the local Codexified-Shannon scan artifacts into the final markdown report. Use after cs-bootstrap and cs-hunt when report.md, executive summary, and consolidated findings need to be created or refreshed.
---

# Shannon Report

## Overview

`shannon-report` assembles the final report from the bootstrap and hunt artifacts. It should feel Shannon-like in structure and rigor, while staying explicit about the local-only validation model.

The report should read like a serious operator handoff: decisive where proof exists, candid where it does not, and useful enough that a strong engineer or security lead could act on it immediately.

## Inputs

- Target repository path
- Active scan output root: `reports/<repo>-<YYYY-MM-DD>/`
- Existing bootstrap artifacts
- Existing hunt artifacts for every completed track

## Required Outputs

- `report/concatenated_findings.md`
- `report.md`
- `report/executive-summary.md`
- `report/findings.json`

## Reporting Rules

1. Read the contracts in `references/report-contract.md` before writing.
2. Build `report/concatenated_findings.md` from `hunt/concatenated_evidence.md` and any per-domain evidence files that need cleanup.
3. Main findings should only include `confirmed` items.
4. Add a `## Prioritized Findings` section that lists confirmed `P0` and `P1` findings only.
5. For each prioritized item, include:
   - the finding title
   - its priority
   - the shortest useful fix direction or remediation starting point
6. If no confirmed `P0` or `P1` findings exist, say that explicitly.
7. `strong-code-evidence` and `needs-runtime-validation` items belong in dedicated follow-up sections, not in the confirmed findings list.
8. Every finding must cite exact evidence paths from prior artifacts or the target repo.
9. Call out explicitly reviewed safe components.
10. Keep the output local-only. No Playwright, browser, or remote-target evidence.

## Synthesis Tips

- Normalize severity and confidence across domains before writing the main report.
- Pull the strongest code references up into the narrative; do not bury the real proof in appendices.
- Make empty or inapplicable domains explicit so the report feels deliberate rather than incomplete.
- When findings are absent, still surface the most important reviewed controls and the biggest remaining uncertainty.
- The prioritized section should be brutally short and action-oriented. It is for the operator who wants to know what to fix first.
- Use `references/writing-playbook.md` when deciding how to phrase evidence, caveats, or remediation priorities.

## Quality Bar

- The report must stand on its own for a human reviewer.
- The executive summary must be short and decision-oriented.
- The appendix should help a later operator reproduce or deepen the scan.
- The body should explain why each finding matters technically, not just restate the title.
