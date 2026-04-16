---
name: cs-bootstrap
description: Create the Shannon-style bootstrap phase for a local-only Codex security scan. Use when starting a scan of a local repository, or whenever pre-recon and recon artifacts need to be generated or refreshed before the hunt phase.
---

# CS Bootstrap

## Overview

`cs-bootstrap` is the local-only replacement for Shannon's pre-recon and recon phases. It builds the baseline intelligence that every later skill depends on.

Prefer code and config as ground truth. Use repo-local evidence, not assumptions, screenshots, or browser automation.

The quality bar is higher than "list the stack." A good bootstrap artifact tells the hunt phase where to spend time, what not to waste time on, and which control planes or trust boundaries are most likely to break.

## Inputs

- Absolute source repository path
- Active scan output root: `reports/<repo>-<YYYY-MM-DD>/`
- Active working copy path: `reports/<repo>-<YYYY-MM-DD>/repo/`

## Tooling

Prefer these tools:

- `rg --files`, `rg -n`
- `fd`
- `tree`
- `bat` or `sed`
- `jq`, `yq`, `sqlite3`
- `semgrep` when it materially reduces guesswork

Do not use Playwright.

## What Good Bootstrap Looks Like

A strong bootstrap pass should:

- identify the real runtime shape of the target, not just its repo labels
- separate user-facing surfaces from local tooling, tests, CI glue, and dead code
- explain where auth, authz, persistence, and external I/O actually happen
- surface unusual or custom code paths that deserve hunt-time attention
- leave behind a clear starting set of files, routes, jobs, and boundaries for later review

If the repo is a monorepo, do not flatten it into one vague application. Name the services, packages, or deployable units and explain how they interact.

## Investigation Heuristics

Start broad, then collapse toward the hot spots.

1. Find the executable entrypoints:
   - CLI entry scripts
   - HTTP route definitions
   - framework route folders
   - workers, cron jobs, queue consumers, and webhooks
2. Identify boundary-defining config:
   - container files
   - compose and infra manifests
   - env loaders and secret readers
   - reverse-proxy and network config
3. Map the security spine:
   - authentication/session setup
   - authorization middleware and policy helpers
   - database and cache layers
   - external HTTP clients and file parsers
4. Highlight the code that looks custom, unusual, or lightly abstracted.
   - bespoke auth
   - homegrown permission checks
   - dynamic query construction
   - parser chains and import/export flows
   - code execution hooks

When framework conventions are unfamiliar, search for multiple route patterns before concluding the app has no endpoints. Prefer several small searches over one giant assumption.

## Workflow

1. Resolve the target repo path and create the scan directories if they do not already exist.
2. Read any target-repo `AGENTS.md` files that apply before doing analysis.
3. Create the working copy at `reports/<repo>-<YYYY-MM-DD>/repo/`.
   - Do not mutate the source repo.
   - All later build/run/probe actions must use the working copy.
4. Write `bootstrap/scope.md` first:
   - classify the target as web app, API, supporting service, CLI-first system, or mixed
   - explicitly note which Shannon-style domains are fully applicable, partially applicable, or likely not applicable
   - note whether localhost validation is realistically available
5. Build the repo inventory:
   - languages, frameworks, package managers
   - routing layers and entrypoints
   - auth/session components
   - data stores, queues, background workers
   - infrastructure and security-relevant config
6. Write `bootstrap/pre_recon.md` using the contract in `references/deliverable-contract.md`.
7. Write `bootstrap/recon.md` using the contract in `references/deliverable-contract.md`.
8. Write `bootstrap/bootstrap_manifest.json` as a compact machine-readable summary for later phases.

## Tips

- Read target-repo docs and `AGENTS.md` files for orientation, but let code and config overrule aspirational README language.
- If the repo contains multiple apps, create a mini service map in your notes before writing the deliverables.
- If you see a package that only feeds internal tooling or tests, mark it explicitly as low-priority rather than silently ignoring it.
- If localhost validation may matter later, note the most plausible start commands, required services, and obvious env files without trying to fully solve runtime at this stage.
- Use `references/recon-playbook.md` when the repo is large, unfamiliar, or surprisingly shaped.

## Quality Bar

- Every important claim should point to a file path, route definition, config key, or code pattern.
- Distinguish between network-reachable surfaces and local-only tooling.
- Call out what is unknown instead of inventing detail.
- The hunt phase should be able to start from your artifacts without re-indexing the repo.
- Record both the source repo path and the working copy path so later phases never confuse them.
- Favor high-signal prose over long inventories. The deliverables should read like an operator handoff, not a dump of grep output.
