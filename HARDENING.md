<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **biomejs--setup-biome/v2.6.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple run: blocks in test.yaml directly interpolate ${{ steps.version.outputs.version }} (a steps.*.outputs.* context) and ${{ env.BIOME_EXPECTED_VERSION }} (an env.* context) inside shell command strings. Per sub-rule (a), ANY ${{ ... }} expression interpolated directly in a run: block is a script-injection finding, regardless of whether the context appears attacker-controlled. Offending lines include:
- `if [ "Version: 2.0.0" == "${{ steps.version.outputs.version }}" ]; then` (test-specific job)
- `if [ "Version: ${{ env.BIOME_EXPECTED_VERSION }}" == "${{ steps.version.outputs.version }}" ]; then` (test-npm, test-pnpm, test-pnpm-9, test-pnpm-workspace-root, test-pnpm-workspace-default-catalog, test-pnpm-workspace-named-catalog, test-yarn, test-bun, test-bun-with-text-lockfile jobs)
- `if [ "Version: 2.0.0-beta.1" == "${{ steps.version.outputs.version }}" ]; then` (test-prerelease job)
These should be replaced with env: variables and double-quoted shell expansions.

Locations:

- `.github/workflows/test.yaml:38`
- `.github/workflows/test.yaml:57`
- `.github/workflows/test.yaml:76`
- `.github/workflows/test.yaml:95`
- `.github/workflows/test.yaml:114`
- `.github/workflows/test.yaml:133`
- `.github/workflows/test.yaml:152`
- `.github/workflows/test.yaml:171`
- `.github/workflows/test.yaml:190`
- `.github/workflows/test.yaml:209`
- `.github/workflows/test.yaml:228`

### missing-permissions (severity: medium)

integrate.yaml has no top-level permissions: key and none of its jobs (build, coding-standards) define a job-level permissions: key. This means the workflow runs with the default token permissions, which may be broader than necessary.

Locations:

- `.github/workflows/integrate.yaml:1`

### missing-permissions (severity: medium)

test.yaml has no top-level permissions: key and none of its jobs (test-specific, test-npm, test-pnpm, test-pnpm-9, test-pnpm-workspace-root, test-pnpm-workspace-default-catalog, test-pnpm-workspace-named-catalog, test-yarn, test-bun, test-bun-with-text-lockfile, test-fallback-latest, test-prerelease) define a job-level permissions: key. This means the workflow runs with the default token permissions, which may be broader than necessary.

Locations:

- `.github/workflows/test.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed all three findings:
1. script-injection (test.yaml): Moved all ${{ steps.version.outputs.version }} and ${{ env.BIOME_EXPECTED_VERSION }} expressions out of run: blocks and into env: blocks as BIOME_VERSION. Shell scripts now reference $BIOME_VERSION and $BIOME_EXPECTED_VERSION as plain environment variables. Applied to all 11 affected steps across test-specific, test-npm, test-pnpm, test-pnpm-9, test-pnpm-workspace-root, test-pnpm-workspace-default-catalog, test-pnpm-workspace-named-catalog, test-yarn, test-bun, test-bun-with-text-lockfile, and test-prerelease jobs.
2. missing-permissions (integrate.yaml): Added `permissions: {}` at the top level.
3. missing-permissions (test.yaml): Added `permissions: {}` at the top level.

