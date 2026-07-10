<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.7.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **biomejs--setup-biome/v2.7.1** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### missing-permissions (severity: medium)

The workflow file has no top-level 'permissions:' key and no job-level 'permissions:' keys on any of its jobs (build, coding-standards). Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions, violating least-privilege.

Locations:

- `.github/workflows/integrate.yaml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level 'permissions:' key and no job-level 'permissions:' keys on any of its many jobs (test-specific, test-npm, test-pnpm, test-pnpm-9, test-pnpm-workspace-root, test-pnpm-workspace-default-catalog, test-pnpm-workspace-named-catalog, test-yarn, test-bun, test-bun-with-text-lockfile, test-from-biome-json, test-from-biome-jsonc, test-fallback-latest, test-prerelease). Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions.

Locations:

- `.github/workflows/test.yaml:1`

### script-injection (severity: high)

Rule (a): Multiple 'Check equality' steps interpolate ${{ steps.version.outputs.version }} and ${{ env.BIOME_EXPECTED_VERSION }} directly inside run: shell scripts. These expressions are substituted by the Actions template engine before the shell sees the command, allowing an attacker who controls the step output or env value to inject arbitrary shell commands. Example offending lines: `if [ "Version: 2.0.0" == "${{ steps.version.outputs.version }}" ]` and `if [ "Version: ${{ env.BIOME_EXPECTED_VERSION }}" == "${{ steps.version.outputs.version }}" ]`. Fix: pass values via env: variables and reference them as quoted shell variables (e.g., "$STEP_OUTPUT").

Locations:

- `.github/workflows/test.yaml:33`
- `.github/workflows/test.yaml:57`
- `.github/workflows/test.yaml:80`
- `.github/workflows/test.yaml:103`
- `.github/workflows/test.yaml:126`
- `.github/workflows/test.yaml:149`
- `.github/workflows/test.yaml:172`
- `.github/workflows/test.yaml:195`
- `.github/workflows/test.yaml:218`
- `.github/workflows/test.yaml:241`
- `.github/workflows/test.yaml:264`
- `.github/workflows/test.yaml:287`
- `.github/workflows/test.yaml:318`

### script-injection (severity: high)

Rule (a): The 'Auto-merge Pull Request' step interpolates ${{ steps.pr.outputs.pull-request-number }} directly inside a run: shell command: `gh pr merge --squash --auto "${{ steps.pr.outputs.pull-request-number }}"`). The Actions template engine substitutes this value before the shell parses the command, so a malicious pull-request number value could inject shell commands. Fix: pass the value via an env: variable and reference it as a quoted shell variable.

Locations:

- `.github/workflows/update-readme-package-version.yaml:43`

## Iteration Notes

### Iteration 1

**Fixes applied:** missing-permissions, script-injection

**Notes:**

Fixed all four findings: (1) Added `permissions: {}` to integrate.yaml top-level; (2) Added `permissions: {}` to test.yaml top-level; (3) Fixed 13 script-injection instances in test.yaml by moving ${{ steps.version.outputs.version }} into env: BIOME_VERSION and referencing it as $BIOME_VERSION in run: blocks — the workflow-level BIOME_EXPECTED_VERSION is already a plain env var so it's safe to use directly in shell; (4) Fixed script-injection in update-readme-package-version.yaml by moving ${{ steps.pr.outputs.pull-request-number }} into env: PR_NUMBER and referencing it as "$PR_NUMBER" in the gh pr merge command.

