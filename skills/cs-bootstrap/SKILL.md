---
name: cs-bootstrap
description: Create the bootstrap phase for a local-only Codex security scan. Use when starting a scan of a local repository, or whenever scoping, inventory, recon, and hunt handoff artifacts need to be generated or refreshed.
---

# CS Bootstrap

## Overview

`cs-bootstrap` prepares the hunt phase. It should leave behind a factual architectural readout, a security-shape map, a short bootstrap synthesis, and one compact JSON handoff.

Prefer code and config as ground truth. Use repo-local evidence, not assumptions, screenshots, or browser automation.

Treat the contract as scaffolding, not a forced template. Keep the required handoff information, but adapt the framing when the repo is a worker, CLI, monorepo, library-heavy service, or anything else that does not behave like a standard web app.

Use `references/cs-bootstrap-playbook.md` for the shared bootstrap method and `bootstrap/handoff.json` shape. Use the matching file under `references/steps/` for the current bootstrap artifact's guidance and output shape.

## Inputs

- Absolute source repository path
- Active scan output root: `reports/<repo>-<YYYY-MM-DD>/`
- Active working copy path: `reports/<repo>-<YYYY-MM-DD>/repo/`

## Tooling

Bootstrap owns tool readiness for the rest of the scan.

Tier A must be present at scan start:

- `rg`
- `fd`
- `jq`
- `yq`
- `git`
- `semgrep`
- `curl`

If any Tier A tool is missing, stop and surface the install command instead of continuing with degraded baseline tooling.

Prefer these core tools during bootstrap work:

- `rg --files`, `rg -n`
- `fd`
- `tree`
- `bat` or `sed`
- `jq`, `yq`, `sqlite3`
- `semgrep` when it materially reduces guesswork
- package-manager-native audit tooling when it is available and low-friction, such as `npm audit`, `pip-audit`, `cargo audit`, or `govulncheck`

After inventory and recon, build one repo-specific Tier B proposal list for optional tools that would materially improve the scan. Keep it to one batched request before hunt starts. Never auto-install. Never re-prompt within the same scan.

That same batched request may include repo-specific dependencies or local services needed to start the app when bootstrap selects `local runtime`, but only when they are ordinary developer tooling or local services reasonable to install on a personal computer.

Prefer repo-native test, lint, and audit commands when the target project already defines them.

Do not use Playwright.

## What Good Bootstrap Looks Like

A strong bootstrap pass should:

- identify the real runtime shape of the target, not just its repo labels
- separate user-facing surfaces from local tooling, tests, CI glue, and dead code
- name the service map or runtime units when the repo has more than one meaningful executable surface
- explain where auth, authz, persistence, and external I/O actually happen
- map the security shape: attacker-controlled inputs, ingress points, egress points, trust boundaries, privileged actions, and assumptions to test
- turn that security-shape work into an explicit `Universal Attack Mapping` step inside `recon.md`
- distinguish framework-default-safe paths from custom-dangerous paths that deserve manual review
- use stack-aware best-practice refs to sharpen expectations when they fit the detected stack
- identify the dependency surface and call out known dependency-risk hotspots when cheap audit tooling is available
- run a lightweight static pressure sweep before hunt begins
- surface hardening leads when controls are missing, fragmented, fragile, or likely to regress even if no exploit is yet confirmed
- leave a runtime recipe handoff for later validation when the repo looks runnable
- leave behind clear high-reward directions for the hunt phase

## Stack-Aware Best-Practice Lens

Use the vendored reference files under `references/security-best-practices/` as a recon-time lens, not as a separate audit mode.

- Match only the refs that fit the detected stack.
- If both backend and frontend materially affect the attack surface, load both sides.
- If no vendored ref matches the stack, say so briefly and continue with direct code- and config-driven recon.
- Distill the refs into:
  - expected safe defaults
  - likely framework-specific footguns
  - custom code paths that bypass those defaults
  - assumptions the hunt phase should actively test

Do not dump a long checklist into the deliverables.

## Workflow

1. Resolve the target repo path and create the scan directories if they do not already exist.
2. Read any target-repo `AGENTS.md` files that apply before doing analysis.
3. Create the working copy at `reports/<repo>-<YYYY-MM-DD>/repo/`.
   - Do not mutate the source repo.
   - All later build, run, test, and probe actions must use the working copy.
4. Write `bootstrap/scope.md` first.
   - classify the target as web app, API, supporting service, CLI-first system, or mixed
   - explicitly note which directed hunt tracks are fully applicable, partially applicable, or largely inapplicable
   - choose one runtime posture: `code-only`, `read-only runtime`, or `local runtime`
   - give a one-line justification and the biggest blocker if the posture is not `local runtime`
   - use `references/steps/scope-playbook.md`
5. Build the repo inventory.
   - languages, frameworks, package managers
   - dependency lockfiles and audit tooling availability
   - routing layers and entrypoints
   - auth/session components
   - data stores, queues, background workers
   - infrastructure and security-relevant config
   - service map or runtime units
   - use `references/steps/inventory-playbook.md`
6. Map the security shape.
   - attacker-controlled inputs and where they first land
   - main ingress and egress points
   - trust boundaries and privileged actions
   - assumptions later phases should try to break
   - write this into `recon.md` as `## Universal Attack Mapping`, not as loose notes
   - use `references/steps/recon-playbook.md`
7. Run a cheap dependency-surface pass.
   - identify lockfiles and direct package ecosystems
   - run audit tooling when it is installed, the lockfile exists, and the command is unlikely to become a time sink
   - if audit tooling is unavailable or the repo shape makes it noisy or misleading, say so explicitly
8. Run a lightweight static pressure sweep.
   - point issues such as weak crypto, hardcoded credentials, insecure config, disabled TLS verification, permissive CORS, dangerous debug flags, or weak randomness
   - secrets clues and dynamic credential handling that deserve later review
   - dependency hotspots worth later follow-up
   - business-logic invariants and state transitions that `general.md` should try to break
   - hardening leads where controls are absent, decentralized, weak by default, or likely to drift over time
   - write the useful results into `recon.md` and `bootstrap.md`; do not create a separate static artifact set
9. Build the Tier B optional-tool proposal.
   - use the detected stack, lockfiles, infra files, and likely validation path
   - produce one `useful for this scan` list with one-line justification per tool
   - include reasonable repo-specific dependencies or local services needed to start the app when the chosen runtime posture is `local runtime`
   - include the install command and what gets skipped if the operator declines
   - do not install tools here; this step only prepares the batched approval request
10. Load only the matching vendored best-practice refs when they help.
11. Write `bootstrap/bootstrap.md`.
   - use `references/steps/bootstrap-playbook.md`
12. Write `bootstrap/handoff.json`.
   - use `references/cs-bootstrap-playbook.md`

## Tips

- Read target-repo docs and `AGENTS.md` files for orientation, but let code and config overrule aspirational README language.
- If the repo contains multiple apps, create a mini service map before writing the deliverables.
- If localhost validation may matter later, note the most plausible start commands, required services, obvious env files, and the best localhost targets without trying to fully solve runtime at this stage.
- Use matching stack refs to sharpen expectations, but rewrite them into repo-specific guidance instead of echoing framework docs.
- Make `## Universal Attack Mapping` feel operational. Hunt should be able to start from it immediately without re-deriving the core abuse paths.
- Keep the static pressure sweep lightweight. It should sharpen hunt, not become a second heavyweight phase.
- Keep noisy intermediate output in `scratch/`; the bootstrap markdown should stay operator-readable.
- When the repo is large or unfamiliar, lean on the step playbooks instead of improvising your own bootstrap structure.

## Quality Bar

- Every important claim should point to a file path, route definition, config key, or code pattern.
- Distinguish between network-reachable surfaces and local-only tooling.
- Call out what is unknown instead of inventing detail.
- The hunt phase should be able to start from your artifacts without re-indexing the repo.
- Favor high-signal prose over long inventories.
