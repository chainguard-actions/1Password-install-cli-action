<!-- markdownlint-disable -->

# Hardening Report: 1Password--install-cli-action/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1Password--install-cli-action/v1.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: `${{ github.action_path }}` is directly interpolated inside a `run:` shell command string in action.yml. Any `${{ ... }}` expression inside a `run:` block is a script injection risk because the expression is substituted into the shell command before the shell parses it, allowing a malicious value to inject arbitrary shell commands. The offending line is: `${{ github.action_path }}/install-cli.sh`. This should be replaced with the safe environment variable `$GITHUB_ACTION_PATH` instead.

Locations:

- `action.yml:18`

### unpinned-uses (severity: high)

The workflow file uses `actions/checkout@v3`, which is pinned to a mutable tag rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling a supply-chain attack. It should be replaced with a full SHA pin, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3`.

Locations:

- `.github/workflows/test.yml:12`

### missing-permissions (severity: medium)

The workflow file `.github/workflows/test.yml` has no top-level `permissions:` key and none of its four jobs (`use-latest-version`, `use-latest-beta-version`, `use-specific-version`, `use-specific-beta-version`) define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal `permissions:` block (e.g. `contents: read`) should be added at the top level or to each job.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

1. action.yml: Replaced `${{ github.action_path }}/install-cli.sh` with `"$GITHUB_ACTION_PATH/install-cli.sh"` to eliminate the script injection risk by using the safe built-in environment variable instead of a template expression. 2. .github/workflows/test.yml: Pinned all four `actions/checkout@v3` references to the immutable SHA `a37ce9120846195fa4ece8f58b268e6043cb2f26` (with `# v3` comment for readability). 3. .github/workflows/test.yml: Added a top-level `permissions: contents: read` block to restrict the workflow token to the minimum permissions needed.

