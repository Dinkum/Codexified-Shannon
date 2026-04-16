# Scope Deliverable Contract

## `bootstrap/scope.md`

Required sections:

1. `# Scope`
2. `## Target Classification`
3. `## In-Scope Shannon Domains`
4. `## Partial Or Inapplicable Domains`
5. `## Expected Validation Modes`
6. `## Working Copy Policy`
7. `## Blocking Assumptions`

Be explicit when a repo is not a traditional web application. This file should prevent the hunt phase from pretending full exploitability where the target shape does not support it.
State clearly that runtime work must happen from `reports/<repo>-<YYYY-MM-DD>/repo/`, not from the original source repo.

# Bootstrap Deliverable Contract

## `bootstrap/pre_recon.md`

Required sections:

1. `# Pre-Recon`
2. `## Executive Summary`
3. `## Technology Stack`
4. `## Application Shape`
5. `## Authentication And Session Surface`
6. `## Data Stores And Sensitive Data Paths`
7. `## External And Internal Entry Candidates`
8. `## High-Risk Code Areas`
9. `## Critical Files For Manual Review`

The goal is to capture the architectural facts that drive later review. Keep it concise, but always anchor claims to the repo.
Also record the source repo path and the working copy path.
Explain the repo's real runtime shape, not just package names.

## `bootstrap/recon.md`

Required sections:

1. `# Recon`
2. `## Attack Surface Summary`
3. `## Network-Reachable Entry Points`
4. `## Authentication Matrix`
5. `## Authorization And Trust Boundaries`
6. `## Input Vector Inventory`
7. `## Background Jobs And Deferred Work`
8. `## Priority Hunt Targets`
9. `## Open Questions`

Focus on code-inferred reachability. Exclude local-only scripts, migrations, and build tooling unless they are reachable from a request path.
Include the most important "why this matters" commentary for the hunt phase, not just endpoint names.

## `bootstrap/bootstrap_manifest.json`

Use this shape:

```json
{
  "repo_name": "example",
  "source_repo_path": "/absolute/path/to/source",
  "working_copy_path": "/absolute/path/to/reports/example-2026-04-15/repo",
  "scan_date": "2026-04-15",
  "languages": ["TypeScript"],
  "frameworks": ["Next.js"],
  "package_managers": ["pnpm"],
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
  "priority_files": ["src/lib/auth.ts"]
}
```

Keep it compact. It is a handoff artifact, not a second report.

## Parity Note

The combined `scope.md`, `pre_recon.md`, `recon.md`, and `bootstrap_manifest.json` files replace Shannon's original pre-recon agent output plus the recon handoff needed by all later agents.
