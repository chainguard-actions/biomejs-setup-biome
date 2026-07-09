<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **biomejs--setup-biome/v2.5.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple `run:` blocks in test.yaml directly interpolate `${{ steps.version.outputs.version }}` and `${{ env.BIOME_EXPECTED_VERSION }}` inside shell command strings (sub-rule a). These expressions flow through YAML template substitution before the shell sees them, enabling script injection. For example: `if [ "Version: 1.5.1" == "${{ steps.version.outputs.version }}" ]; then`. These should be moved to `env:` variables and referenced as quoted shell variables.

Locations:

- `.github/workflows/test.yaml:35`
- `.github/workflows/test.yaml:61`
- `.github/workflows/test.yaml:88`
- `.github/workflows/test.yaml:115`
- `.github/workflows/test.yaml:142`
- `.github/workflows/test.yaml:169`
- `.github/workflows/test.yaml:196`
- `.github/workflows/test.yaml:228`

### missing-permissions (severity: medium)

integrate.yaml has no top-level `permissions:` key and none of its jobs (build, coding-standards) define job-level permissions. The workflow runs with default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/integrate.yaml:1`

### missing-permissions (severity: medium)

test.yaml has no top-level `permissions:` key and none of its jobs (test-specific, test-npm, test-pnpm, test-pnpm-9, test-yarn, test-bun, test-bun-with-text-lockfile, test-fallback-latest, test-prerelease) define job-level permissions. All jobs run with default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/test.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed all 3 findings across 2 workflow files:

1. **script-injection** (test.yaml, 8 locations): Moved all `${{ steps.version.outputs.version }}` and `${{ env.BIOME_EXPECTED_VERSION }}` expressions out of `run:` shell strings and into `env:` blocks as `BIOME_VERSION` and `BIOME_EXPECTED_VERSION` variables. Shell scripts now reference these as `$BIOME_VERSION` and `$BIOME_EXPECTED_VERSION`.

2. **missing-permissions** (integrate.yaml): Added `permissions: {}` at the top level of the workflow.

3. **missing-permissions** (test.yaml): Added `permissions: {}` at the top level of the workflow.

