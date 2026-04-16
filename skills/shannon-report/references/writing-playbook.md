# Writing Playbook

Use this when turning dense artifacts into a report that still feels sharp.

## Executive Summary

Prioritize:

- what was actually confirmed
- what was strongly suggested but not proven
- what architectural themes dominate risk
- what the operator should fix first

Avoid:

- long inventories
- repeating section titles as prose
- pretending weak evidence is strong evidence

## Main Findings

Each confirmed finding should answer:

- what breaks
- where it breaks
- how the evidence proves it
- why it matters in practice

Keep the proof chain visible. One short paragraph of high-signal explanation beats several vague paragraphs.

## Prioritized Findings

This section is for confirmed `P0` and `P1` items only.

For each item, give the reader:

- what to fix first
- where to start fixing it
- why it cannot wait

Good format:

- `P0: Missing object ownership check on invoice download`
  - Urgency: cross-tenant data exposure is directly reachable
  - Fix direction: move ownership enforcement into the service layer that loads invoices, not just the route decorator

Keep this section short. It is a triage list, not the full report repeated.

## Handling Weak Or Partial Evidence

- `strong-code-evidence`: explain the path clearly and say what runtime step would close the loop
- `needs-runtime-validation`: explain what prevented proof and what the next operator should do
- inapplicable domain: say why the domain is inapplicable for this repo shape

## Reviewed And Found Safe

This section matters. It should show:

- which tempting surfaces were reviewed
- what control held up
- where future regressions would be dangerous

## Remediation Framing

Prefer:

- remove or narrow the dangerous path
- move checks closer to the sink or protected action
- enforce normalization before allowlists
- centralize authz or session logic when it is fragmented

Avoid generic boilerplate that could apply to any report.
