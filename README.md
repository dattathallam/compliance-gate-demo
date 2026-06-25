# Compliance Gate Demo

A minimal workflow that shows the [`gh-action-compliance-gate`](../gh-action-compliance-gate)
action enforcing a compliant version before a third-party action runs.

Workflow ([.github/workflows/gate-demo.yml](.github/workflows/gate-demo.yml)):

1. `actions/checkout@v4`
2. `dattathallam/gh-action-compliance-gate@main` — the gate
3. `dattathallam/gh-action-composite@v1` — runs **only if the gate passed**

## What you should see

`gh-action-composite` has tags `v1.0.1, v1.0.0`. With the demo mock server (which blocks
`v1.0.1`):

- `gacpm list dattathallam/gh-action-composite@v1` → `[v1.0.1, v1.0.0]`
- `filterVersions` blocks → `["v1.0.1"]`
- 0th remaining → `v1.0.0`
- `curation` approves `v1.0.0`
- gate unzips `v1.0.0` into `_work/_actions/dattathallam/gh-action-composite/v1/`, then the
  composite greeter runs that enforced version

Because `gh-action-composite` is a **composite** action (read from disk at step time), the swap
is fully effective — the workflow actually runs the enforced `v1.0.0`.

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
