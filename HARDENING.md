<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **biomejs--setup-biome/v2.7.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in test.yaml directly interpolate GitHub Actions expressions inside shell commands. Specifically, `${{ steps.version.outputs.version }}` (a steps.* context value) and `${{ env.BIOME_EXPECTED_VERSION }}` (an env.* context value) are interpolated directly into shell `if` comparisons. Any expression inside a `run:` block is a script-injection risk regardless of whether the context appears controlled, because YAML template substitution occurs before the shell ever sees the value. Affected steps: 'Check equality' in jobs test-specific (line ~35), test-npm (~62), test-pnpm (~88), test-pnpm-9 (~114), test-pnpm-workspace-root (~140), test-pnpm-workspace-default-catalog (~166), test-pnpm-workspace-named-catalog (~192), test-yarn (~218), test-bun (~244), test-bun-with-text-lockfile (~270), test-from-biome-json (~296), test-from-biome-jsonc (~322), test-prerelease (~358).

Locations:

- `.github/workflows/test.yaml:35`
- `.github/workflows/test.yaml:62`
- `.github/workflows/test.yaml:88`
- `.github/workflows/test.yaml:114`
- `.github/workflows/test.yaml:140`
- `.github/workflows/test.yaml:166`
- `.github/workflows/test.yaml:192`
- `.github/workflows/test.yaml:218`
- `.github/workflows/test.yaml:244`
- `.github/workflows/test.yaml:270`
- `.github/workflows/test.yaml:296`
- `.github/workflows/test.yaml:322`
- `.github/workflows/test.yaml:358`

### script-injection (severity: high)

Sub-rule (a): The 'Auto-merge Pull Request' step in update-readme-package-version.yaml directly interpolates `${{ steps.pr.outputs.pull-request-number }}` inside a `run:` shell command: `gh pr merge --squash --auto "${{ steps.pr.outputs.pull-request-number }}"`). A steps.* output value is interpolated directly into the shell before quoting, which is a script-injection risk.

Locations:

- `.github/workflows/update-readme-package-version.yaml:40`

### missing-permissions (severity: medium)

integrate.yaml has no top-level `permissions:` key and none of its jobs (build, coding-standards) define job-level `permissions:`. Without explicit permissions, the workflow inherits the repository default (which may be write-all), granting broader access than necessary.

Locations:

- `.github/workflows/integrate.yaml:1`

### missing-permissions (severity: medium)

test.yaml has no top-level `permissions:` key and none of its many jobs define job-level `permissions:`. Without explicit permissions, the workflow inherits the repository default (which may be write-all), granting broader access than necessary.

Locations:

- `.github/workflows/test.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed all 4 findings across 3 workflow files:

1. test.yaml (script-injection): All 13 'Check equality' steps had ${{ steps.version.outputs.version }} and ${{ env.BIOME_EXPECTED_VERSION }} moved from run: shell scripts into step-level env: blocks. They are now referenced as plain shell variables $ACTUAL_VERSION and $BIOME_EXPECTED_VERSION respectively.

2. update-readme-package-version.yaml (script-injection): The 'Auto-merge Pull Request' step had ${{ steps.pr.outputs.pull-request-number }} moved from the run: shell command into the env: block as PR_NUMBER, referenced as "$PR_NUMBER" in the shell.

3. integrate.yaml (missing-permissions): Added `permissions: {}` at the top level of the workflow.

4. test.yaml (missing-permissions): Added `permissions: {}` at the top level of the workflow.

