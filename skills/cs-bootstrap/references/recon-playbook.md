# Recon Playbook

Use this when the target repo is large, multi-service, or initially confusing.

## Questions To Answer Early

- What actually starts in production or local dev?
- Which package or directory owns the main request path?
- Where do auth, authz, persistence, and external calls live?
- What code is custom enough that framework defaults no longer protect it?
- What looks reachable from a request, job, or callback, and what is only tooling?

## Good High-Signal Searches

- route definitions and framework entrypoints
- env and secret loaders
- middleware registration
- DB client initialization
- HTTP client initialization
- file parser and archive handling
- markdown, template, or rich-text rendering
- worker, cron, queue, event, and webhook handlers

## Priority Signals

Spend more time where you find:

- custom auth/session code
- custom policy or ownership checks
- shell execution or raw query assembly
- dynamic path joins, plugin loading, or code execution hooks
- third-party callback handling
- import/export flows, especially compressed or serialized formats
- administrative or support-only flows

## Bootstrap Writing Tips

- Prefer "the request path enters here, then crosses this helper, then reaches this store/client" over "the app uses Express and Postgres."
- If the target is not a classic web app, say that directly and explain the consequences for later domains.
- Call out likely false trails so the hunt phase can avoid them.

