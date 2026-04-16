# Codexified-Shannon 
A Codex version of https://github.com/KeygraphHQ/shannon

## Prerequisites
1. Codex 
2. ripgrep jq yq semgrep 
`brew install ripgrep jq yq semgrep`

## Usage 
While in this repo run
`codex -m gpt-5.4 -c model_reasoning_effort="xhigh" --full-auto -c sandbox_workspace_write.network_access=true`

Tell Codex to scan a repo
> Scan ~/Desktop/Repo

Codex will then generate a report at repo-<DATE> in the reports/ folder

## Limitations 
We skip browser simulation 
We do local only code analysis 
