---
name: cs-hunt
description: Run the local-only Codex hunt phase across a data-flow step, five directed security domains, a general step, and a final optimization step. Use after cs-bootstrap when you need step writeups and one aggregate hunt synthesis for a repository scan.
---

# CS Hunt

## Overview

`cs-hunt` consumes the bootstrap artifacts, runs one data-flow step, five directed security domains, a general step, and a final optimization step, then ends with one aggregate hunt synthesis.

The goal is not to spray generic scanners at the repo. The goal is to develop strong, attack-relevant theories, pressure-test them, and leave behind clear operator-facing step writeups.

Confirmed vulnerabilities are not the only valid output. Material hardening improvements, fragile controls, and regression-prone defenses should be carried forward when they materially improve security posture, even if no exploit is confirmed.

Use `references/cs-hunt-playbook.md` for the shared hunt method, then load the matching file under `references/tracks/` for the current step's heuristics and output shape.

## Inputs

- Source repository path
- Active scan output root: `reports/<repo>-<YYYY-MM-DD>/`
- Active working copy path: `reports/<repo>-<YYYY-MM-DD>/repo/`
- Bootstrap artifacts:
  - `bootstrap/scope.md`
  - `bootstrap/inventory.md`
  - `bootstrap/recon.md`
  - `bootstrap/bootstrap.md`
  - `bootstrap/handoff.json`

Treat `bootstrap/handoff.json` as the primary structured targeting map, but read all bootstrap markdown before starting any step.
Start from `bootstrap/recon.md`'s `## Universal Attack Mapping`, `bootstrap/recon.md`'s `## Static Pressure Points`, and `bootstrap/bootstrap.md`'s hunt directions before you decide where to spend time.
Hunt is expected to use the runtime posture declared in `bootstrap/scope.md` and `bootstrap/handoff.json`.

## Hunt Step Order

Unless the user explicitly narrows scope, execute all eight hunt steps in this order:

1. `data-flow.md`
2. `injection.md`
3. `xss.md`
4. `auth.md`
5. `ssrf.md`
6. `authz.md`
7. `general.md`
8. `optimization.md`

`data-flow.md` runs first and maps attacker-controlled sources to sensitive sinks or protected actions, then judges whether the actual guards or sanitizers are sufficient in context.

`general.md` catches real issues that do not fit cleanly into the five directed lenses. It is also the home for business-logic security testing: infer important invariants from the code, then try to violate them with the cheapest credible local proof path.

`optimization.md` runs last and looks only for `LOW RISK HIGH REWARD` wins: improvements that reduce cost, duplication, latency, or scaling pain without requiring risky rewrites.

## Enumeration First

Before writing a step file:

- run targeted `rg` searches using the baseline patterns from the current step playbook
- run `semgrep` when it is available and meaningful for the stack
- read `references/framework-checklists.md` when `bootstrap/handoff.json` identifies a matching framework
- save raw support under `hunt/artifacts/<step>/` only when it materially helps

Enumeration widens coverage; it does not prove a finding.

## Validation Ladder

Use this as a practical decision tree:

1. Run static enumeration and direct tracing from code.
2. Read existing tests that touch the path.
3. If runtime posture is `read-only runtime` or `local runtime`, use existing tests, harmless local inspection commands, or other non-mutating execution that sharpens the path.
4. If runtime posture is `local runtime`, write a small repo-local harness when isolating the helper is cheaper than booting a stack, or use `curl` or equivalent against a localhost service started from the working copy when endpoint behavior matters.
5. If none of the above safely closes the loop, carry the path forward as `needs-runtime-validation` and say exactly why.

Do not force every rung when it is clearly inapplicable. Do record what was tried.

## Hunt Mindset

- Start with the hottest surfaces from bootstrap, not with equal effort per step.
- Use `data-flow.md` to route the highest-value source-to-sink or source-to-boundary paths into the later domain steps.
- Consume bootstrap's universal attack mapping and static pressure points before chasing domain-specific patterns. They should tell you which attacker-controlled inputs, boundaries, guards, assumptions, and privileged actions matter most.
- Trace end to end.
- Keep asking what an attacker would actually do next.
- Use the declared runtime posture instead of improvising your own.
- Use runtime only to sharpen or clear findings, not as a substitute for understanding the code.
- Explicitly document safe components that looked promising but held up under review.
- Surface hardening notes whenever a control is missing, fragmented, opt-in, weak by default, or likely to regress.
- When a step stays quiet, explain why.

## Runtime Validation Ideas

If the repo can be run locally from the working copy, useful validation moves include:

- start the app and hit the suspected flow with a minimal request
- run or extend an existing test with a focused repro
- seed temporary data in the copied repo
- write a tiny harness into `scratch/` to isolate a parser, validator, or helper
- add short-lived local instrumentation in the copied repo to confirm a code path

Store raw support under `hunt/artifacts/<step>/` when it materially supports the step writeup.

Honor bootstrap's runtime posture when deciding whether to do this work. If bootstrap selected `code-only`, stay at the code-evidence ceiling and say so directly.

## Required Outputs

- `hunt/data-flow.md`
- `hunt/injection.md`
- `hunt/xss.md`
- `hunt/auth.md`
- `hunt/ssrf.md`
- `hunt/authz.md`
- `hunt/general.md`
- `hunt/optimization.md`
- `hunt/hunt.md`

Optional raw support:

- `hunt/artifacts/<step>/...`

## Method

1. Read all bootstrap artifacts before starting any step.
   - Pay particular attention to `bootstrap/recon.md`'s `## Universal Attack Mapping` and `## Static Pressure Points`.
2. Read `references/cs-hunt-playbook.md` for the shared hunt method and the output shape for `hunt/hunt.md`.
3. Read the matching step playbook under `references/tracks/` before writing that step.
4. Run `data-flow.md` first.
   - map sources, transforms, guards, sinks, and protected actions
   - judge the sufficiency of the actual sanitization or guard in context
   - route the strongest paths into the later domain steps
5. For the five directed domain steps, check `bootstrap/scope.md` before deep review.
6. Run enumeration first, then trace, then validate when needed.
7. Write each step as one operator-facing markdown artifact.
8. Use `general.md` to consume whatever the universal attack mapping, static pressure sweep, or data-flow pass surfaced that does not fit cleanly into injection, xss, auth, ssrf, or authz.
   - this is where business-logic security testing lives
   - infer important invariants from code and state transitions
   - design concrete violation attempts
   - validate them with the cheapest credible local proof path
9. Run `optimization.md` last.
   - look only for `LOW RISK HIGH REWARD` wins
   - focus on avoidable O(n) or repeated work, needless abstraction cost, duplication, unnecessary serialization or parsing, and other efficiency gains that do not require risky rewrites
   - prefer changes that are cheap to explain and cheap to implement
10. After the eight hunt steps finish, write `hunt/hunt.md` as the aggregate hunt synthesis.
   - include step outcomes
   - include cross-step chains
   - include the biggest unresolved paths
   - include notable hardening, trust-model, and optimization carry-forward notes
   - include short, concrete suggested next moves for the final handoff
11. Never mutate the original source repo.
