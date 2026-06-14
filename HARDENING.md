<!-- markdownlint-disable -->

# Hardening Report: 1Password--install-cli-action/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **1Password--install-cli-action/v1.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression `${{ github.action_path }}` is interpolated directly inside a `run:` shell command string in action.yml. Any `${{ ... }}` expression directly in a `run:` block is a script-injection risk because the value is substituted into the shell command string before the shell parses it, allowing an attacker who can influence the value to inject arbitrary shell commands. The safe alternative is to use the pre-set `$GITHUB_ACTION_PATH` environment variable instead: `"$GITHUB_ACTION_PATH"/install-cli.sh`.

Locations:

- `action.yml:18`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Replaced `${{ github.action_path }}/install-cli.sh` with `"$GITHUB_ACTION_PATH/install-cli.sh"` in action.yml line 18. The `$GITHUB_ACTION_PATH` environment variable is automatically set by GitHub Actions and is safe to use directly in shell scripts, unlike the `${{ github.action_path }}` expression which is interpolated into the shell command string before parsing and could allow script injection.

