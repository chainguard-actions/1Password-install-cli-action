<!-- markdownlint-disable -->

# Hardening Report: 1Password--install-cli-action/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **1Password--install-cli-action/v4.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions by mutable tag instead of a full 40-character commit SHA, making them vulnerable to supply-chain attacks.

- check-fallback-versions.yml: `actions/checkout@v6` and `actions/setup-node@v6` (tag refs)
- pr-check-signed-commits.yml: `1Password/check-signed-commits-action@v1` (tag ref)
- test.yml: `actions/checkout@v6` used in both the `use-docker-hub-fallback` and `use-baked-in-fallback` jobs (tag refs)

Locations:

- `.github/workflows/check-fallback-versions.yml:10`
- `.github/workflows/check-fallback-versions.yml:11`
- `.github/workflows/pr-check-signed-commits.yml:17`
- `.github/workflows/test.yml:76`
- `.github/workflows/test.yml:100`

### script-injection (severity: high)

Multiple `run:` blocks in test.yml directly interpolate GitHub Actions expressions inside shell command strings (rule a), allowing an attacker who controls matrix values or step outputs to inject arbitrary shell commands.

1. `use-docker-hub-fallback` — "Capture expected version" step:
   - `version=$(curl -s https://app-updates.agilebits.com/latest | jq -r '${{ matrix.versionPath }}')`  — `${{ matrix.versionPath }}` interpolated directly into shell
   - `echo "Expected ${{ matrix.channel }} version: $version"` — `${{ matrix.channel }}` interpolated directly into shell

2. `use-docker-hub-fallback` — "Assert the fallback resolved" step:
   - `run: ./test/assert-version.sh "${{ steps.expected.outputs.version }}"` — step output interpolated directly into shell

3. `use-baked-in-fallback` — "Read expected baked-in version" step:
   - `version=$(grep "ReleaseChannel.${{ matrix.key }}]" ...)` — `${{ matrix.key }}` interpolated directly into shell
   - `echo "Expected baked-in ${{ matrix.channel }} version: $version"` — `${{ matrix.channel }}` interpolated directly into shell

4. `use-baked-in-fallback` — "Assert the baked-in version was installed" step:
   - `run: ./test/assert-version.sh "${{ steps.expected.outputs.version }}"` — step output interpolated directly into shell

Locations:

- `.github/workflows/test.yml:80`
- `.github/workflows/test.yml:81`
- `.github/workflows/test.yml:87`
- `.github/workflows/test.yml:104`
- `.github/workflows/test.yml:105`
- `.github/workflows/test.yml:116`

### github-env-injection (severity: high)

Two `run:` blocks in test.yml write a shell variable (`$version`) to `$GITHUB_OUTPUT` without sanitization. The variable is derived from untrusted matrix context values (`${{ matrix.versionPath }}` and `${{ matrix.key }}`) via command substitution, so a newline injected into the value could poison subsequent steps reading from `$GITHUB_OUTPUT`.

1. `use-docker-hub-fallback` — "Capture expected version" step:
   `echo "version=$version" >> "$GITHUB_OUTPUT"` where `$version` comes from `jq -r '${{ matrix.versionPath }}'`

2. `use-baked-in-fallback` — "Read expected baked-in version" step:
   `echo "version=$version" >> "$GITHUB_OUTPUT"` where `$version` comes from `grep "ReleaseChannel.${{ matrix.key }}]"`

Neither write is preceded by the required `printf '%s' "$version" | tr -d '\n\r'` sanitization step.

Locations:

- `.github/workflows/test.yml:82`
- `.github/workflows/test.yml:106`

### missing-permissions (severity: medium)

Two workflow files have no top-level `permissions:` block and no job-level `permissions:` blocks, meaning jobs run with the default (potentially broad) token permissions.

- test.yml: no permissions declared at top level or in any of its six jobs.
- check-fallback-versions.yml: no permissions declared at top level or in its single job.

Locations:

- `.github/workflows/test.yml:1`
- `.github/workflows/check-fallback-versions.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, github-env-injection, missing-permissions

**Notes:**

Fixed all four findings across three workflow files:

1. **unpinned-uses**: Pinned actions/checkout@v6 (→ d23441a48e516b6c34aea4fa41551a30e30af803), actions/setup-node@v6 (→ 249970729cb0ef3589644e2896645e5dc5ba9c38), and 1Password/check-signed-commits-action@v1 (→ ed2885f3ed2577a4f5d3c3fe895432a557d23d52) to their full commit SHAs.

2. **script-injection**: Moved all ${{ matrix.versionPath }}, ${{ matrix.channel }}, ${{ matrix.key }}, and ${{ steps.expected.outputs.version }} expressions from run: shell strings into env: blocks, referencing them as plain environment variables in the shell scripts.

3. **github-env-injection**: Added `safe=$(printf '%s' "$version" | tr -d '\n\r')` sanitization before writing to $GITHUB_OUTPUT in both the use-docker-hub-fallback and use-baked-in-fallback capture steps.

4. **missing-permissions**: Added `permissions: contents: read` top-level blocks to test.yml and check-fallback-versions.yml.

