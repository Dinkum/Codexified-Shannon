# CS Hunt Playbook

Use this file for the shared hunt method. Then load the matching file under `references/tracks/` for the current step's heuristics and output shape.

## Shared Method

1. Read all bootstrap artifacts before starting any step:
   - `bootstrap/scope.md`
   - `bootstrap/inventory.md`
   - `bootstrap/recon.md`
   - `bootstrap/bootstrap.md`
   - `bootstrap/handoff.json`
2. Read the runtime posture from `bootstrap/scope.md` and `bootstrap/handoff.json`, then honor it throughout hunt.
3. Start from `bootstrap/recon.md`'s `## Universal Attack Mapping`, `bootstrap/recon.md`'s `## Static Pressure Points`, and `bootstrap/bootstrap.md`'s investigation directions.
4. Run `data-flow.md` first and route the strongest paths into the later domain steps.
5. For the directed domain steps, check `bootstrap/scope.md` before deep review.
6. Treat each hunt domain as a full step with three mandatory consecutive phases:
   - `Investigation`: use the current step playbook to explore the domain thoroughly, cast a wide net, and append each plausible issue, general concern, or hardening suggestion to `hunt/artifacts/<step>/candidates_log.md` immediately when discovered
   - `Verification`: begin only after the exploration pass is complete and the candidate set for the step has been appended to `hunt/artifacts/<step>/candidates_log.md`; then work through candidates one by one, verify or kill them, and append each outcome to `hunt/artifacts/<step>/verified_log.md` immediately before checking the next candidate, including verified general concerns and hardening suggestions
   - `Writeup`: write one operator-facing markdown file for the step, synthesized from both logs and what actually happened during investigation and verification
7. During the `Verification` phase, if a new plausible issue or concern appears:
   - append it to `candidates_log.md`
   - investigate it in the same step
   - record the outcome in `verified_log.md`
   - do not treat the initial `candidates_log.md` contents as a closed set
8. Do not move to the next hunt step until the current domain has been properly explored, the candidate set has been pressure-tested, and the writeup is complete.
9. Write one operator-facing markdown file per step.
   - every subsection in the step markdown should be a flat list, not prose blocks
   - use `## What We Looked Into` to name the reviewed surfaces and, when helpful, briefly note which ones held up as safe
   - treat hardening as a first-class output, not as leftover report polish
   - any material issue with a concrete, bounded path you can point to belongs in `## Issues We Found And Verified`
   - any broader hardening, maintenance, trust-boundary, posture, duplication, bloat, or codebase-wide concern belongs in `## General Concerns And Hardening Suggestions We Verified`
   - every concern should include the concern, where it lives, why it matters, and the suggested hardening step
   - reserve `## Summary` for short orientation bullets, not for hiding the actual issue load
10. Run `optimization.md` last and keep it focused on `LOW RISK HIGH REWARD` wins rather than broad rewrite ideas.
11. After all eight hunt steps finish, write `hunt/hunt.md` as the aggregate hunt synthesis.

Treat each hunt step as a real domain checkpoint with three explicit consecutive phases: investigate, verify, then write. Do not do one giant hunt pass and dump `data-flow.md`, the five directed steps, `general.md`, and `optimization.md` at the end. Finish the current domain to a solid, satisfactory, operator-usable standard before continuing in order. If a later step materially changes an earlier conclusion, update the earlier step explicitly.

Treat logging as live within each phase:

- during investigation, discover one candidate -> append it immediately
- only after the exploration pass is complete should verification begin
- during verification, check one candidate -> append the outcome immediately
- do not batch a whole investigation or verification pass in memory and only write the logs afterward

The required hunt sequence is:

1. `data-flow.md`
2. `injection.md`
3. `xss.md`
4. `auth.md`
5. `ssrf.md`
6. `authz.md`
7. `general.md`
8. `optimization.md`
9. `hunt/hunt.md`

## Scope Gate

For the directed domain steps:

- If the step is fully or partially applicable, continue normally.
- If the step is largely inapplicable, do a light confirming sweep and write a short, credible step file rather than a forced deep dive.

`data-flow.md` does not use a classic scope gate. It should still map source-to-sink or source-to-boundary paths even when some later domain steps will be quiet.

`general.md` does not need a strict scope gate. It is the catch-all lane for meaningful issues outside the five directed lenses and the home for business-logic security testing.

`optimization.md` does not need a classic scope gate either. It is the final engineering-efficiency lane for `LOW RISK HIGH REWARD` wins only.

## Enumeration Expectations

Before writing a step file:

1. Run targeted `rg` searches using the baseline patterns from the current step playbook.
2. Run `semgrep` when it is installed and meaningful for the stack.
   - Point `HOME`, `SEMGREP_USER_HOME`, `XDG_CONFIG_HOME`, and `XDG_CACHE_HOME` at a writable scan-local directory under `scratch/` so it does not try to write to `~/.semgrep`.
3. Store raw outputs under `hunt/artifacts/<step>/` only when they materially help.
4. Cite the sweep results or explicitly state that the baseline searches did not produce useful hits.

Good enumeration widens coverage; it does not replace reasoning.

## Step Scratchpads

Every hunt step must keep two append-only scratchpads under `hunt/artifacts/<step>/`:

- `candidates_log.md`
  - live append-only scratchpad for plausible issues, general concerns, and hardening suggestions
  - append each candidate immediately when you discover it; do not wait until the end of investigation
  - do not rewrite it into a polished report
- `verified_log.md`
  - live append-only scratchpad for outcomes on each candidate
  - append whether the candidate was kept, downgraded to a concern, confirmed as a hardening suggestion, disproved, or left unresolved immediately after that candidate is checked
  - if verification uncovers a new candidate, append that candidate immediately to `candidates_log.md`, then keep going until the new path is resolved or explicitly left open

These logs are working memory for hunt, not end-user prose. The final step markdown should be written only after the investigation and verification substeps are both complete.

## Validation Ladder

Use this as a practical decision tree:

1. Run static enumeration and direct tracing from code.
2. Read existing tests that touch the path.
3. If runtime posture is `read-only runtime` or `local runtime`, use existing tests, harmless local inspection commands, or other non-mutating execution that sharpens the path.
4. If runtime posture is `local runtime`, write a small repo-local harness when isolating the helper is cheaper than booting a stack, or use `curl` or equivalent against a localhost service started from the working copy when endpoint behavior matters.
5. If none of the above safely closes the loop, carry the path forward as `code-supported` and say exactly why runtime confirmation is still missing.

Do not force every rung when it is clearly inapplicable. Do record what was tried.
Do not use the original source repo's `.venv`, `node_modules`, compiled helpers, or already-running services to satisfy these runtime steps.

## Verification Notes

When moving a candidate from `candidates_log.md` to `verified_log.md`, capture enough to make the decision auditable:

- what path or invariant you checked
- what evidence changed your confidence
- whether it landed as an issue, a concern, a safe call, or a dead end
  - `issue`: specific, bounded, and pointable
  - `concern`: broader verified hardening or maintenance guidance, even when it spans multiple files or the wider codebase
- what follow-up would be needed if runtime proof is still missing

## Trace Blocks

For every non-trivial finding and every material `safe` determination, leave behind a concise trace block:

- `Source`: where attacker-controlled input or privilege enters
- `Transforms`: meaningful helpers, parsing, normalization, or branching along the path
- `Sink Or Protected Action`: the dangerous operation or sensitive action
- `Guard`: what blocks exploitation or enforces the boundary
- `Verdict`: `safe`, `runtime-confirmed`, or `code-supported`

Each line should cite file and line references when the path depends on real code.

## Confidence Guidance

- `high`
  - the path is clear, the mitigation is absent or mismatched, and scope is known
- `medium`
  - the path is strong but one material assumption remains
- `low`
  - the idea is plausible but the path or scope is still blurry

When in doubt, round down.

## Good Runtime Moves

- hit the narrowest request path possible
- isolate one parser or helper with a tiny harness
- reuse existing tests where practical
- prefer localhost services over broad environment recreation
- keep the runtime environment inside the working copy rather than borrowing it from the source repo
- log the exact command or harness path if it would help a later operator pick up the thread
- stay within the runtime posture bootstrap declared

## Bad Runtime Moves

- booting the entire stack when one helper test would do
- treating a startup failure as proof a finding is false
- writing invasive patches when a trace or targeted repro is enough
- ignoring a `code-only` posture and trying to stand the app up anyway

## Near Misses And Chains

- Keep a short near-miss list when a candidate looked plausible but failed under review.
- Note cross-step chains when they are real.
- Keep hardening notes close to the step that discovered them so the final report has real provenance instead of vague cleanup advice.
- Use `general.md` to consume leftovers from `## Universal Attack Mapping`, `## Static Pressure Points`, and `data-flow.md` that do not fit cleanly into injection, xss, auth, ssrf, or authz.
- Use `optimization.md` to capture low-risk engineering wins that should be surfaced to the operator even when they are not security findings.

## Output Shape

### `hunt/hunt.md`

Required sections:

1. `# Hunt`
2. `## Summary`
3. `## Cross-Step Chains`
4. `## Highest-Value Unresolved Paths`
5. `## General Concerns Carried Forward`
6. `## LOW RISK HIGH REWARD Wins`
7. `## Suggested Next Moves`
8. `## Misc Notes`

All sections should be flat lists.

Only put real cross-step chains in `## Cross-Step Chains`. Do not invent them to fill the section.

`## Suggested Next Moves` should be short and concrete. It is the main hunt-side input to the final report's `## What's Next`.
