<!-- markdownlint-disable -->

# Hardening Report: 1Password--install-cli-action/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1Password--install-cli-action/v3.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Workflow files reference external actions using mutable tag refs instead of pinned full-length SHA commit hashes. In .github/workflows/test.yml, `actions/checkout@v6` is used four times (once per job). In .github/workflows/pr-check-signed-commits.yml, `1Password/check-signed-commits-action@v1` is used. Tags are mutable and can be moved to point to a different (potentially malicious) commit at any time, enabling supply-chain attacks.

Locations:

- `.github/workflows/test.yml:11`
- `.github/workflows/test.yml:20`
- `.github/workflows/test.yml:30`
- `.github/workflows/test.yml:40`
- `.github/workflows/pr-check-signed-commits.yml:12`

### missing-permissions (severity: medium)

The workflow file .github/workflows/test.yml has no top-level `permissions:` key and none of its four jobs (use-latest-version, use-latest-beta-version, use-specific-version, use-specific-beta-version) define a `permissions:` block. Without explicit permissions, the workflow inherits the default repository permissions, which may grant excessive write access to the GITHUB_TOKEN.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

1. Pinned all 4 occurrences of `actions/checkout@v6` in .github/workflows/test.yml to full SHA `d23441a48e516b6c34aea4fa41551a30e30af803` with `# v6` comment. 2. Pinned `1Password/check-signed-commits-action@v1` in .github/workflows/pr-check-signed-commits.yml to full SHA `ed2885f3ed2577a4f5d3c3fe895432a557d23d52` with `# v1` comment. 3. Added top-level `permissions: contents: read` block to .github/workflows/test.yml — the minimum permission needed for the checkout steps. The pr-check-signed-commits.yml already had job-level permissions (contents: read, pull-requests: write) so no change was needed there.

