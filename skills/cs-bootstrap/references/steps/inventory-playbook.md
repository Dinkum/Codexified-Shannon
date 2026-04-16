# Inventory Playbook

Use this step to capture the factual repo context that drives later review.

This is the second bootstrap checkpoint. Use `scope.md` as the guardrail, gather the factual runtime and repo-shape context, then write `inventory.md` before doing the deeper attack mapping in `recon.md`.

## Questions To Answer Early

- What actually starts in production or local dev?
- Which package or directory owns the main request path or job flow?
- Where do auth, authz, persistence, and external calls live?
- What code is clearly runtime-relevant versus test-only, local tooling, or dead code?
- Which package managers, lockfiles, and dependency audit paths exist?
- Which files or components deserve first-pass manual review?

## Good High-Signal Searches

- route definitions and framework entrypoints
- env and secret loaders
- middleware registration
- framework auth wrappers and policy helpers
- DB client initialization
- HTTP client initialization
- dependency lockfiles and package manifests
- file parser and archive handling
- markdown, template, or rich-text rendering
- worker, cron, queue, event, and webhook handlers

## Output Shape

### `bootstrap/inventory.md`

Required sections:

1. `# Inventory`
2. `## Executive Summary`
3. `## Technology Stack`
4. `## Application Shape`
5. `## Service Map Or Runtime Units`
6. `## Authentication And Session Surface`
7. `## Data Stores And Sensitive Data Paths`
8. `## Dependency Surface`
9. `## External And Internal Entry Candidates`
10. `## Top Review Targets`

The goal is to leave later phases with a factual map, not a giant package dump.

`## Executive Summary`, `## Application Shape`, and `## Service Map Or Runtime Units` should be clean enough to seed the final report's `Context (Bootstrap/Meta)` -> `What We Scanned` section.
