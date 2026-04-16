# Codexified-Shannon 
A Codex version of (and inspired by) https://github.com/KeygraphHQ/shannon
Performs a white box security analysis your local web app code.

## Prerequisites
1. Codex 
2. ripgrep jq yq semgrep 
`brew install ripgrep jq yq semgrep`

## Usage 
While in this repo run
`codex -m gpt-5.4 -c model_reasoning_effort="high" --full-auto -c sandbox_workspace_write.network_access=true`

Tell Codex to scan a repo
> Scan ~/Desktop/Repo

Codex will then generate a report at repo-DATE in the reports/ folder

## Security 
* By running this tool, you agree you own, trust, and have permission to test the target repo code 
* Running on other people's code could be dangerous due to prompt injection risks

## Limitations 
* We skip browser simulation 
* We do local only code analysis 

# Notes 
Codex "security-best-practices" skill is used in some palces for references https://github.com/openai/skills/tree/main/skills/.curated/security-best-practices