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
6. Run enumeration first, then trace, then validate when needed.
7. Write one operator-facing markdown file per step.
   - treat hardening as a first-class output, not as leftover report polish
   - when a control is missing, fragmented, opt-in, weak by default, or likely to regress, record the hardening note even if no exploit is confirmed
8. Run `optimization.md` last and keep it focused on `LOW RISK HIGH REWARD` wins rather than broad rewrite ideas.
9. After all eight hunt steps finish, write `hunt/hunt.md` as the aggregate hunt synthesis.

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
3. Store raw outputs under `hunt/artifacts/<step>/` only when they materially help.
4. Cite the sweep results or explicitly state that the baseline searches did not produce useful hits.

Good enumeration widens coverage; it does not replace reasoning.

## Validation Ladder

Use this as a practical decision tree:

1. Run static enumeration and direct tracing from code.
2. Read existing tests that touch the path.
3. If runtime posture is `read-only runtime` or `local runtime`, use existing tests, harmless local inspection commands, or other non-mutating execution that sharpens the path.
4. If runtime posture is `local runtime`, write a small repo-local harness when isolating the helper is cheaper than booting a stack, or use `curl` or equivalent against a localhost service started from the working copy when endpoint behavior matters.
5. If none of the above safely closes the loop, carry the path forward as `needs-runtime-validation` and say exactly why.

Do not force every rung when it is clearly inapplicable. Do record what was tried.

## Trace Blocks

For every non-trivial finding and every material `safe` determination, leave behind a concise trace block:

- `Source`: where attacker-controlled input or privilege enters
- `Transforms`: meaningful helpers, parsing, normalization, or branching along the path
- `Sink Or Protected Action`: the dangerous operation or sensitive action
- `Guard`: what blocks exploitation or enforces the boundary
- `Verdict`: `safe`, `confirmed`, `strong-code-evidence`, or `needs-runtime-validation`

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
2. `## Step Outcomes`
3. `## Cross-Step Chains`
4. `## Highest-Value Unresolved Paths`
5. `## Hardening And Trust-Model Carry-Forward`
6. `## LOW RISK HIGH REWARD Wins`
7. `## Suggested Next Moves`
8. `## Overall Hunt Verdict`

Only put real cross-step chains in `## Cross-Step Chains`. Do not invent them to fill the section.

`## Suggested Next Moves` should be short and concrete. It is the main hunt-side input to the final report's `## What's Next`.
