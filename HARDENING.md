<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **biomejs--setup-biome/v2.5.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in `.github/workflows/test.yaml` directly interpolate `${{ steps.version.outputs.version }}` and `${{ env.BIOME_EXPECTED_VERSION }}` inside shell `if` comparisons. These expressions are substituted by the GitHub Actions template engine before the shell ever sees the string, meaning a malicious value in a step output or env context could inject arbitrary shell commands. Rule (a) violation — `${{ ... }}` expressions must not appear inside `run:` scripts; use `env:` variables and reference them as `"$VAR"` instead.

Affected lines ("Check equality" steps):
- test-specific job: `if [ "Version: 1.5.1" == "${{ steps.version.outputs.version }}" ]`
- test-npm job: `if [ "Version: ${{ env.BIOME_EXPECTED_VERSION }}" == "${{ steps.version.outputs.version }}" ]`
- test-pnpm job: same pattern
- test-pnpm-9 job: same pattern
- test-yarn job: same pattern
- test-bun job: same pattern
- test-bun-with-text-lockfile job: same pattern
- test-prerelease job: `if [ "Version: 2.0.0-beta.1" == "${{ steps.version.outputs.version }}" ]`

Locations:

- `.github/workflows/test.yaml:34`
- `.github/workflows/test.yaml:62`
- `.github/workflows/test.yaml:88`
- `.github/workflows/test.yaml:114`
- `.github/workflows/test.yaml:140`
- `.github/workflows/test.yaml:166`
- `.github/workflows/test.yaml:192`
- `.github/workflows/test.yaml:222`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. `write` access to contents). A minimal explicit `permissions:` block (e.g. `permissions: read-all` or specific scopes) should be added.

Locations:

- `.github/workflows/integrate.yaml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A minimal explicit `permissions:` block should be added.

Locations:

- `.github/workflows/test.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed all three findings:
1. script-injection (test.yaml, 8 locations): Moved all `${{ steps.version.outputs.version }}` and `${{ env.BIOME_EXPECTED_VERSION }}` expressions out of `run:` shell scripts into `env:` blocks. Each 'Check equality' step now declares `ACTUAL_VERSION: ${{ steps.version.outputs.version }}` in its `env:` block and references it as `"$ACTUAL_VERSION"` in the shell script. The test-npm step also declares its local `BIOME_EXPECTED_VERSION` override in the `env:` block.
2. missing-permissions (integrate.yaml): Added `permissions: {}` at the top level after the `on:` block.
3. missing-permissions (test.yaml): Added `permissions: {}` at the top level after the `on:` block.

