<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.7.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **biomejs--setup-biome/v2.7.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/integrate.yaml:1`
- `.github/workflows/test.yaml:1`

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are interpolated directly inside `run:` shell command strings. In test.yaml, every 'Check equality' step uses `${{ steps.version.outputs.version }}` and/or `${{ env.BIOME_EXPECTED_VERSION }}` directly in shell `if` comparisons (e.g. `if [ "Version: 2.0.0" == "${{ steps.version.outputs.version }}" ]`). The `steps.*.outputs.*` and `env.*` contexts flow through YAML template substitution before the shell sees them, making them vulnerable to injection if the values contain shell metacharacters. All 13 'Check equality' steps across jobs test-specific, test-npm, test-pnpm, test-pnpm-9, test-pnpm-workspace-root, test-pnpm-workspace-default-catalog, test-pnpm-workspace-named-catalog, test-yarn, test-bun, test-bun-with-text-lockfile, test-from-biome-json, test-from-biome-jsonc, and test-prerelease are affected.

Locations:

- `.github/workflows/test.yaml:35`
- `.github/workflows/test.yaml:63`
- `.github/workflows/test.yaml:90`
- `.github/workflows/test.yaml:118`
- `.github/workflows/test.yaml:146`
- `.github/workflows/test.yaml:174`
- `.github/workflows/test.yaml:202`
- `.github/workflows/test.yaml:230`
- `.github/workflows/test.yaml:258`
- `.github/workflows/test.yaml:286`
- `.github/workflows/test.yaml:314`
- `.github/workflows/test.yaml:342`
- `.github/workflows/test.yaml:378`

### script-injection (severity: high)

Sub-rule (a): In update-readme-package-version.yaml, the 'Auto-merge Pull Request' step directly interpolates `${{ steps.pr.outputs.pull-request-number }}` inside a `run:` shell command: `gh pr merge --squash --auto "${{ steps.pr.outputs.pull-request-number }}"`). The `steps.*.outputs.*` context is substituted by the YAML template engine before the shell processes the string, allowing injection if the output value contains shell metacharacters.

Locations:

- `.github/workflows/update-readme-package-version.yaml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** missing-permissions, script-injection

**Notes:**

Fixed all three findings across three workflow files:

1. integrate.yaml: Added `permissions: {}` top-level block.

2. test.yaml: Added `permissions: {}` top-level block. Fixed all 13 'Check equality' steps by moving `${{ steps.version.outputs.version }}` into `env: ACTUAL_VERSION` and referencing it as `$ACTUAL_VERSION` in the shell script. For test-npm, also moved the step-level `${{ env.BIOME_EXPECTED_VERSION }}` reference to use the env var directly. The top-level `BIOME_EXPECTED_VERSION` env var is already available to all other jobs without needing expression interpolation in run blocks.

3. update-readme-package-version.yaml: Fixed the 'Auto-merge Pull Request' step by moving `${{ steps.pr.outputs.pull-request-number }}` into `env: PR_NUMBER` and referencing it as `"$PR_NUMBER"` in the shell command.

