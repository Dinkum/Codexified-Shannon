---
name: cs-hunt
description: Run the local-only Shannon hunt phase across injection, xss, auth, authz, and ssrf. Use after cs-bootstrap when you need domain findings, validation notes, and per-track artifacts for a repository scan.
---

# CS Hunt

## Overview

`cs-hunt` is the local-only replacement for Shannon's vulnerability-analysis and exploitation phases. It consumes the bootstrap artifacts, walks the five Shannon domains, and preserves Shannon's analysis -> queue -> conditional exploitation pattern.

The goal is not to spray generic scanners at the repo. The goal is to develop strong, attack-relevant theories, pressure-test them, and promote only the survivors into queues and evidence.

## Inputs

- Source repository path
- Active scan output root: `reports/<repo>-<YYYY-MM-DD>/`
- Active working copy path: `reports/<repo>-<YYYY-MM-DD>/repo/`
- Existing bootstrap artifacts:
  - `bootstrap/scope.md`
  - `bootstrap/pre_recon.md`
  - `bootstrap/recon.md`
  - `bootstrap/bootstrap_manifest.json`

## Track Order

Unless the user explicitly narrows scope, execute all five tracks:

1. `injection`
2. `xss`
3. `auth`
4. `ssrf`
5. `authz`

Shannon ran these in parallel. This Codex port may execute them sequentially, but every track must still produce its own artifacts and its own queue decision.

## Validation Ladder

Use the strongest safe proof available:

1. Source-to-sink or guard-path proof from code
2. Existing unit or integration tests in the repo
3. Small repo-local harnesses or non-destructive scripts
4. `curl` or equivalent against an already-running localhost service started from the working copy

If a finding still needs a real runtime to prove impact, label it `needs-runtime-validation`.

Do not use Playwright.

## Hunt Mindset

- Start with the hottest surfaces from bootstrap, not with equal effort per domain.
- Trace end to end. A true candidate should connect an input or privilege boundary to a sink or protected action through real code.
- Keep asking "what would an attacker actually do next?" If you cannot answer that, the candidate probably needs more work.
- Use runtime only to sharpen or clear findings, not as a substitute for understanding the code.
- Explicitly document safe components that looked promising but held up under review.

## Prioritization Heuristics

Bias the hunt toward:

- custom auth/session code
- custom authorization helpers and ownership checks
- import/export, upload/download, parser, and archive flows
- outbound URL handling and callback features
- raw SQL, shell execution, serialization, markdown/rendering, and template boundaries
- admin paths, support tools, and background jobs triggered from normal app behavior

Move lower when the path is mostly framework boilerplate with strong default defenses and no custom escape hatches.

## Runtime Validation Ideas

If the repo can be run locally from the working copy, useful validation moves include:

- start the app and hit the suspected flow with a minimal request
- run or extend an existing test with a focused repro
- seed temporary data in the copied repo
- write a tiny harness into `scratch/` to isolate a parser, validator, or helper
- add short-lived local instrumentation in the copied repo to confirm a code path

Always prefer the smallest validation that proves or clears the claim.

## False-Positive Discipline

Do not queue a finding just because:

- a sink exists somewhere in the repo
- a route looks scary but is unreachable in the real runtime shape
- comments or docs imply behavior that code does not implement
- code is dead, test-only, or strictly local tooling
- a framework helper sounds unsafe but is actually wrapped by a stronger guard

If you are uncertain, downgrade confidence or keep it out of the queue until the path is clearer.

## Required Outputs

For each track, create:

- `hunt/<track>/analysis.md`
- `hunt/<track>/exploitation_queue.json`
- `hunt/<track>/evidence.md`
- `hunt/<track>/status.json`

After all requested tracks finish, create:

- `hunt/summary.md`
- `hunt/concatenated_evidence.md`

## Finding States

- `confirmed`
- `strong-code-evidence`
- `needs-runtime-validation`
- `safe`

## Method

1. Read the bootstrap artifacts before starting any track.
2. Use `references/domain-contracts.md` for the domain checklist and output rules.
   - Use `references/investigation-playbook.md` when you need deeper guidance on tracing, confidence, or runtime tactics.
3. For each domain, write the analysis first, then generate the queue.
4. If the queue is non-empty, perform local validation and write evidence. If the queue is empty, write an evidence file that records the skip reason.
5. Keep exact file paths, route names, guards, sinks, and config keys in every finding.
6. Separate safe-but-reviewed components from true findings.
7. Do not collapse multiple domains into one file. Preserve per-track boundaries.
8. Never mutate the original source repo. Any build, start, test, or probe action must use `reports/<repo>-<YYYY-MM-DD>/repo/`.
9. Avoid overfitting to one hypothesis. When a strong path closes, pivot to the next hottest path instead of forcing the same finding.
