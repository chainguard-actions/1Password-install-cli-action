<!-- markdownlint-disable -->

# Hardening Report: 1Password--install-cli-action/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1Password--install-cli-action/v2.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both workflow files reference external actions using mutable tags instead of full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the tag is moved or the upstream repository is compromised.

- `.github/workflows/test.yml`: `actions/checkout@v4` (used 5 times)
- `.github/workflows/pr-check-signed-commits.yml`: `1Password/check-signed-commits-action@v1`

All `uses:` references should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/test.yml:9`
- `.github/workflows/test.yml:19`
- `.github/workflows/test.yml:30`
- `.github/workflows/test.yml:41`
- `.github/workflows/test.yml:52`
- `.github/workflows/pr-check-signed-commits.yml:11`

### missing-permissions (severity: medium)

`.github/workflows/test.yml` has no top-level `permissions:` key and none of its jobs (`jest-tests`, `use-latest-version`, `use-latest-beta-version`, `use-specific-version`, `use-specific-beta-version`) define a `permissions:` block. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions, which may be overly broad (e.g. `write` on `contents`). A minimal `permissions:` block (e.g. `contents: read`) should be added at the top level or on each job.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

1. Pinned all 5 occurrences of `actions/checkout@v4` in `.github/workflows/test.yml` to SHA `34e114876b0b11c390a56381ad16ebd13914f8d5` (v4). 2. Pinned `1Password/check-signed-commits-action@v1` in `.github/workflows/pr-check-signed-commits.yml` to SHA `ed2885f3ed2577a4f5d3c3fe895432a557d23d52` (v1). 3. Added a top-level `permissions: contents: read` block to `.github/workflows/test.yml` to restrict the default token permissions to the minimum required.

