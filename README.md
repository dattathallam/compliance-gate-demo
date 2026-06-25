# Compliance Gate Demo

A minimal workflow that shows the [`gh-action-compliance-gate`](../gh-action-compliance-gate)
action enforcing a compliant version before a third-party action runs.

Workflow ([.github/workflows/gate-demo.yml](.github/workflows/gate-demo.yml)):

1. `actions/checkout@v4`
2. `dattathallam/gh-action-compliance-gate@main` — the gate
3. `dattathallam/gh-action-go@v1` — runs **only if the gate passed**

## What you should see

`gh-action-go` has tags `v1.0.2, v1.0.1, v1.0.0`. With the demo mock server:

- `gacpm list dattathallam/gh-action-go@v1` → `[v1.0.2, v1.0.1, v1.0.0]`
- `filterVersions` blocks the first → `["v1.0.2"]`
- 0th remaining → `v1.0.1`
- `curation` approves `v1.0.1` (only `v1.0.2` URLs are rejected)
- gate unzips `v1.0.1` into `_work/_actions/dattathallam/gh-action-go/v1/`, then `gh-action-go` runs

To see the **stop** path, force a rejection (e.g. point `api-base-url` at a curation that rejects
the selected version) — the gate exits non-zero and `gh-action-go` never runs.

## Prerequisites

- Self-hosted runner online (your Mac) with `gacpm`, `gh`, `jq`, `curl` on PATH
  (see the gate action's README for the `gacpm` install).
- The mock curation server reachable at `api-base-url` (the `mock-server`, exposed via ngrok).

## Run it

Push this repo, then:

```bash
gh workflow run "Compliance Gate Demo"
gh run watch
```

Or trigger from the **Actions** tab → *Compliance Gate Demo* → *Run workflow*.
