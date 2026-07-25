<!-- markdownlint-disable -->

# Hardening Report: 1Password--install-cli-action/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1Password--install-cli-action/v4.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses action references pinned to mutable tags instead of immutable full-length commit SHAs, making the workflow vulnerable to supply-chain attacks if the tag is moved. Failing references:
- `uses: 1Password/check-signed-commits-action@v1` (mutable version tag)
- `uses: actions/checkout@v6` (mutable version tag, appears 4 times across 4 jobs)

Locations:

- `.github/workflows/pr-check-signed-commits.yml:12`
- `.github/workflows/test.yml:11`
- `.github/workflows/test.yml:20`
- `.github/workflows/test.yml:30`
- `.github/workflows/test.yml:40`

### missing-permissions (severity: medium)

The workflow file test.yml has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the default repository permissions (which may include write access), violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

1. Pinned `1Password/check-signed-commits-action@v1` → SHA `ed2885f3ed2577a4f5d3c3fe895432a557d23d52` in pr-check-signed-commits.yml. 2. Pinned all 4 occurrences of `actions/checkout@v6` → SHA `d23441a48e516b6c34aea4fa41551a30e30af803` in test.yml, preserving the original tag as an inline comment. 3. Added top-level `permissions: contents: read` to test.yml to enforce least privilege.

