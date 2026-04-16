# CS Bootstrap Playbook

Use this file for the shared bootstrap method. Pair it with the step playbooks under `references/steps/`.

## Shared Method

1. Resolve the target path and read any applicable target-repo `AGENTS.md` files.
2. Create the per-scan working copy under `reports/<repo>-<YYYY-MM-DD-HH-MM>/repo/`.
3. Write the bootstrap artifacts in order:
   - `scope.md`
   - `inventory.md`
   - `recon.md`
   - `bootstrap.md`
   - `handoff.json`
4. Keep the original source repo read-only. Any build, run, test, or probe action must use the working copy.
5. Do not borrow the source repo's runtime state after the copy exists.
   - no source-repo `.venv`
   - no source-repo `node_modules/.bin`
   - no source-repo build outputs or helper binaries
   - no source-repo services started outside the working copy

Treat each bootstrap artifact as a checkpoint. Do not do one giant recon pass and dump `scope.md`, `inventory.md`, `recon.md`, and `bootstrap.md` in one swoop at the end. Bring the current artifact to a solid, satisfactory, operator-usable state, write it, then continue. If later evidence materially changes an earlier file, update it explicitly instead of silently relying on stale bootstrap output.

## Ground Truth Rules

- Prefer code and config over README claims.
- Distinguish network-reachable surfaces from local-only tooling, tests, and one-off scripts.
- Call out uncertainty directly instead of inventing detail.
- Keep the markdown artifacts operator-readable. Put noisy command output in `scratch/`.

## Tool Readiness And Tiering

Bootstrap owns tool readiness for the rest of the scan.

- Treat Tier A as required baseline tooling: `rg`, `fd`, `jq`, `yq`, `git`, `semgrep`, `curl`.
- If a Tier A tool is missing, attempt the install command so approval or denial is explicit. If the install is denied or fails, stop early instead of improvising around it.
- When invoking `semgrep`, point `HOME`, `SEMGREP_USER_HOME`, `XDG_CONFIG_HOME`, and `XDG_CACHE_HOME` at a writable scan-local directory under `scratch/` so it does not try to write to `~/.semgrep`.
- Build one Tier B `useful for this scan` list after inventory and recon, then attempt one batched install command before hunt starts.
- Keep Tier B repo-tied and practical. Propose only the tools that materially improve this scan.
- When bootstrap chooses `local runtime`, the same batched proposal may include repo-specific dependencies or local services needed to start the app, but only when they are ordinary developer tooling or local services reasonable to install on a personal computer.
- Prefer repo-native test, lint, and audit commands when the target project already defines them.
- Do not merely print suggested install commands and wait. Attempt the batched install so approval or denial is explicit, then continue based on the result. Never re-prompt within the same scan.
- When bootstrap chooses `local runtime`, do not stop at install. Attempt a minimal startup or smoke check before hunt begins so later verification has a real runtime foothold.

Strong Tier B candidates include:

- `gitleaks` for baseline secret scanning
- `osv-scanner` when lockfiles are present
- `tokei` when scan-size triage is useful
- `ast-grep` when syntax-aware matching materially helps
- `sqlite3` when SQLite files are present
- `bandit` or `gosec` when Python or Go are in scope
- `hadolint` or `trivy` when containers or IaC are materially in scope
- `docker`, `docker compose`, `httpie`, or `mitmproxy` when the likely local validation path would benefit from them

## Stack-Aware Best-Practice Lens

Use the vendored files under `references/security-best-practices/` as a recon-time lens, not as a separate audit mode.

- Load only the refs that match the detected stack.
- If both backend and frontend materially affect the attack surface, load both sides.
- Distill them into:
  - expected safe defaults
  - likely framework-specific footguns
  - custom code paths that bypass those defaults
  - assumptions hunt should actively test

Do not dump framework checklists into the bootstrap artifacts.

## Dependency Surface

- Identify package managers, manifests, and lockfiles.
- Run package-manager-native audit tooling only when it is installed, the repo shape supports it, and the command is unlikely to become a time sink.
- Treat audit output as a lead source, not as proof of exploitable app risk.
- If audit tooling is unavailable or too noisy for the repo shape, say so explicitly in `inventory.md`.

## Lightweight Static Pressure Sweep

Before hunt begins, run a lightweight static sweep to sharpen where later effort should go.

- point issues: weak crypto, hardcoded credentials, insecure configuration, disabled TLS verification, permissive CORS, dangerous debug flags, weak randomness
- secrets clues: obvious tokens, suspicious env fallbacks, dynamic credential builders, repo-local custom secret formats
- dependency hotspots: reachable-looking risky packages, dangerous plugin systems, or vulnerable components worth manual follow-up
- business-logic pressure points: ownership assumptions, state transitions, approval paths, invite flows, billing or quota checks, import/export paths

Keep this lightweight and repo-specific. The goal is to give hunt better starting hypotheses, not to create a second static-analysis product inside bootstrap.

## Runtime Handoff

When the repo looks runnable, leave later phases a lightweight validation handoff.

If bootstrap chose `local runtime`, try to make that handoff concrete before hunt starts:

- attempt the batched install for any needed runtime dependencies or local services
- attempt a minimal startup or smoke check from the working copy
- prefer the narrowest viable start path rather than full environment recreation
- if the attempt succeeds, record the actual command run and the actual localhost target reached
- if the attempt fails or becomes misleading, record the blockers and downgrade the posture when appropriate

For all repos that look runnable, leave later phases:

- chosen runtime posture
- likely start commands
- obvious env or config files
- supporting services
- best localhost targets
- what looks destructive, expensive, or not worth starting yet
- actual startup command and actual localhost target when bootstrap successfully smoke-started the app
- concrete blockers when bootstrap tried and failed to stand it up

Do not turn bootstrap into full environment recreation. The goal is a minimal, credible runtime foothold, not a full dev environment.

## Tier B Install Hand-Off

Before hunt starts, package optional-tool recommendations into one batched install attempt.

- call the list `useful for this scan`
- give one-line repo-tied justification per tool
- include reasonable repo-specific dependencies or local services needed for `local runtime` when they materially affect the scan
- attempt the install command for the operator's platform instead of only printing it
- if the install is denied or fails, say what coverage or convenience gets skipped
- do not split this into multiple prompts or multiple piecemeal install attempts

If `local runtime` was chosen, use the result of this batched install attempt to try a minimal startup or smoke check before hunt starts. Record what actually worked and what did not.

## Output Shape

### `bootstrap/handoff.json`

Use this shape:

```json
{
  "repo_name": "example",
  "source_repo_path": "/absolute/path/to/source",
  "working_copy_path": "/absolute/path/to/reports/example-2026-04-16-13-55/repo",
  "scan_date": "2026-04-16",
  "languages": ["TypeScript"],
  "frameworks": ["Next.js"],
  "runtime_posture": "local runtime",
  "runtime_units": [
    {
      "name": "api",
      "kind": "http-service",
      "path": "apps/api",
      "externally_reachable": true
    }
  ],
  "entrypoints": [
    {
      "kind": "http",
      "path": "/api/users/:id",
      "file": "src/app/api/users/[id]/route.ts",
      "auth": "user"
    }
  ],
  "auth_components": ["src/lib/auth.ts"],
  "data_stores": ["postgres"],
  "attacker_controlled_inputs": ["request body on POST /api/users"],
  "trust_boundaries": ["user -> http handler", "service -> postgres"],
  "privileged_actions": ["creates user records"],
  "assumptions_to_test": ["only authenticated users reach /api/users/:id"],
  "top_review_targets": ["src/lib/auth.ts"],
  "best_practice_refs": ["javascript-typescript-nextjs-web-server-security.md"],
  "likely_start_commands": ["npm ci", "npm run dev"],
  "actual_start_command": "npm run dev",
  "supporting_services": ["postgres via docker compose"],
  "localhost_targets": ["http://127.0.0.1:3000/api/users/123"],
  "actual_localhost_target": "http://127.0.0.1:3000/healthz",
  "runtime_blockers": [],
  "env_files": [".env.example"]
}
```

Keep it compact. It is a handoff index, not a second report.
