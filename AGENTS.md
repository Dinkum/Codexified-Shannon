# Codexified Shannon

Use this package to run a local-only, white-box security review against a repository on disk. The workflow mirrors Shannon's phase model and deliverable discipline, but it does not use Playwright, browser simulation, or remote targets.

## Source Safety

The source repository provided by the user is read-only for scan purposes.

- At the start of every scan, create a per-scan working copy at `reports/<repo>-<YYYY-MM-DD>/repo/`.
- Never edit, build into, clean, install into, or otherwise mutate the original source repository.
- Any command that can write files, install dependencies, generate build output, start services, run migrations, or create temp state must run against the copied repo under `reports/<repo>-<YYYY-MM-DD>/repo/`.
- Use the original source repo for reference only when needed; use the copied repo for execution.

If local runtime validation is useful, start the app or services from the copied repo and probe that local instance only.

## Analysis Standard

This bundle is intended to produce deep, attack-minded analysis, not a shallow checklist pass.

- Do not stop at naming frameworks, routes, or files. Trace behavior through guards, helpers, services, sinks, and data stores.
- Use the bootstrap phase to narrow where the real risk is, then spend hunt time on the hottest code paths rather than distributing attention evenly.
- Treat `semgrep`, `rg`, and framework conventions as accelerants, not answers. Every material finding should survive direct code review.
- Try to disprove your own theory before promoting it. If a mitigation is present, document it and downgrade or clear the candidate.
- Review safe components on purpose. A strong scan should show what was examined and ruled out, not only what looked scary.
- When a domain is structurally inapplicable, say so early and explain why.

## Evidence Rules

- Every finding should cite exact files and, when possible, exact lines, routes, guards, or configuration keys.
- One finding should correspond to one distinct flaw path or one distinct access-control failure. Do not merge unrelated issues into a vague blob.
- Keep the proof chain explicit:
  - source or entrypoint
  - transformation or guard path
  - sink or protected action
  - mitigation check
  - conclusion
- Runtime evidence is stronger than static evidence, but static evidence is still valuable when the repo shape prevents safe local proof.
- If a finding is only plausible, leave it out of the queue or mark it conservatively.

## Investigation Priorities

Bias effort toward these surfaces first:

- custom authentication and session logic
- authorization middleware, policy helpers, and ownership checks
- file upload, download, import, export, archive, and parser flows
- outbound fetchers, URL processors, webhooks, image fetchers, and callback logic
- shelling out, raw SQL, templating, markdown/rendering, serialization, and plugin execution
- admin paths, support tooling, debug toggles, and background jobs reachable from normal app flows

Then widen out into lower-signal surfaces only after the high-risk paths have been reviewed.

## Tools

- Repo inspection: `rg`, `fd`, `tree`, `bat`
- Structured config parsing: `jq`, `yq`, `sqlite3`
- Security/code search: `semgrep`
- Language validation when relevant: `pytest`, `pyright`, `ruff`, `go test`, `golangci-lint`, `shellcheck`

Do not use Playwright. Do not rely on browser automation. Do not scan public internet targets from this bundle.

## Entry Flow

When the user says `scan <LOCAL REPO PATH>`:

1. Resolve the path to an absolute local repository path.
2. Run a cheap preflight:
   - confirm the path exists
   - read any applicable `AGENTS.md`
   - identify whether the target is a deployable web app/API, a supporting service, or mostly local tooling
   - note any obvious coverage limits before deeper work begins
3. Derive the repository slug and today's date as `YYYY-MM-DD`.
4. Create `reports/<repo>-<YYYY-MM-DD>/` with these subdirectories:
   - `repo/`
   - `bootstrap/`
   - `hunt/`
   - `hunt/injection/`
   - `hunt/xss/`
   - `hunt/auth/`
   - `hunt/ssrf/`
   - `hunt/authz/`
   - `report/`
   - `scratch/`
   - `artifacts/`
5. Copy the source repository into `reports/<repo>-<YYYY-MM-DD>/repo/`.
   - Preserve the repo contents as faithfully as practical.
   - Treat the copied repo as the only writable execution workspace for the scan.
6. Run the skills in this exact order:
   - `$cs-bootstrap`
   - `$cs-hunt`
   - `$shannon-report`
7. Finish with the final markdown deliverable at `reports/<repo>-<YYYY-MM-DD>/report.md`.

Do not skip a phase. Do not start `$cs-hunt` until `$cs-bootstrap` has written its required artifacts. Do not start `$shannon-report` until `$cs-hunt` has completed all five hunt tracks.

## Shannon Parity Model

This package maps to Shannon's original agent pipeline like this:

1. `pre-recon` -> `$cs-bootstrap` / `bootstrap/pre_recon.md`
2. `recon` -> `$cs-bootstrap` / `bootstrap/recon.md`
3. `injection-vuln` -> `$cs-hunt` / `hunt/injection/analysis.md`
4. `xss-vuln` -> `$cs-hunt` / `hunt/xss/analysis.md`
5. `auth-vuln` -> `$cs-hunt` / `hunt/auth/analysis.md`
6. `ssrf-vuln` -> `$cs-hunt` / `hunt/ssrf/analysis.md`
7. `authz-vuln` -> `$cs-hunt` / `hunt/authz/analysis.md`
8. `injection-exploit` -> `$cs-hunt` / `hunt/injection/evidence.md`
9. `xss-exploit` -> `$cs-hunt` / `hunt/xss/evidence.md`
10. `auth-exploit` -> `$cs-hunt` / `hunt/auth/evidence.md`
11. `ssrf-exploit` -> `$cs-hunt` / `hunt/ssrf/evidence.md`
12. `authz-exploit` -> `$cs-hunt` / `hunt/authz/evidence.md`
13. `report` -> `$shannon-report` / `report.md`

The local-only adaptation is this:

- analysis still produces per-domain queues
- queue length still decides whether exploitation-style validation runs
- exploitation becomes local validation and evidence generation instead of browser-led live exploitation
- report assembly still happens after concatenating domain evidence

## Local Validation Ideas

When the codebase supports it, local validation can include:

- starting the app or supporting services from the working copy
- running targeted unit or integration tests
- issuing `curl` or HTTP client requests against localhost
- creating temporary seed data inside the copied repo
- writing tiny harnesses or repro scripts into `scratch/`
- instrumenting logs or adding temporary assertions in the copied repo only

Keep all such activity minimal, reversible, and scoped to proving or clearing a candidate.

## Phase Contract

### 1. `$cs-bootstrap`

Purpose: Shannon-style pre-recon plus recon for a local repository.

Required outputs:

- `repo/` working copy of the scanned repository
- `bootstrap/pre_recon.md`
- `bootstrap/recon.md`
- `bootstrap/bootstrap_manifest.json`
- `bootstrap/scope.md`

Minimum content:

- source repo path and working copy path
- technology stack and repo shape
- auth/session components
- route and entrypoint inventory inferred from code
- trust boundaries and data stores
- critical files for manual review
- prioritized attack surface for the hunt phase

### 2. `$cs-hunt`

Purpose: local-only replacement for Shannon's parallel vuln-analysis and exploitation stages.

Execution rules:

- Treat the following as the five required Shannon hunt tracks:
  - `injection`
  - `xss`
  - `auth`
  - `ssrf`
  - `authz`
- You may work sequentially in this Codex port, but every track must produce its own artifacts.
- For each track, do Shannon's two-step pattern:
  - write the analysis deliverable
  - write an exploitation queue
  - if the queue has one or more in-scope candidates, run local validation and write evidence
  - if the queue is empty, write a skip record and do not fabricate evidence
- Any build, server start, test run, or probing step must run from `reports/<repo>-<YYYY-MM-DD>/repo/`, never from the original source repo.
- Prefer code-backed proof. If a localhost dev server or existing test harness is available, you may use it. Never use Playwright and never assume a live remote target exists.
- Any claim that requires runtime confirmation but cannot be safely proven locally must be labeled `needs-runtime-validation`.

Required outputs per track:

- `hunt/<track>/analysis.md`
- `hunt/<track>/exploitation_queue.json`
- `hunt/<track>/evidence.md`
- `hunt/<track>/status.json`

Required aggregate output:

- `hunt/summary.md`
- `hunt/concatenated_evidence.md`

Finding states:

- `confirmed`
- `strong-code-evidence`
- `needs-runtime-validation`
- `safe`

### 3. `$shannon-report`

Purpose: synthesize bootstrap and hunt artifacts into a Shannon-style final report.

Required outputs:

- `report/concatenated_findings.md`
- `report.md`
- `report/executive-summary.md`
- `report/findings.json`

Reporting rules:

- Build `report/concatenated_findings.md` from the per-domain evidence files before writing the final report.
- Include a `## Prioritized Findings` section in `report.md`.
  - List only confirmed `P0` and `P1` findings there.
  - For each item, include a short fix direction or the best remediation starting point.
  - If there are no confirmed `P0` or `P1` findings, say so explicitly.
- Separate confirmed findings from runtime-dependent hypotheses.
- Cite exact files, routes, sinks, or guards for every material claim.
- Include secure-by-design components that were explicitly reviewed and found safe.
- Keep the report local-only: no browser screenshots, no remote network evidence, no Playwright transcripts.

## Local Scope Rules

- White-box only. Operate on source the user has locally.
- Optional runtime validation is limited to localhost or repo-owned test harnesses already available to the operator.
- No public internet exploitation.
- No destructive actions against the target repo unless the operator explicitly asks for them.
