<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **biomejs--setup-biome/v2.6.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Multiple run: blocks in test.yaml directly interpolate ${{ steps.version.outputs.version }} and ${{ env.BIOME_EXPECTED_VERSION }} expressions inside shell commands (sub-rule a). These expressions are substituted by the GitHub Actions template engine before the shell sees them, allowing injection of shell metacharacters. For example: `if [ "Version: 2.0.0" == "${{ steps.version.outputs.version }}" ]; then` and `if [ "Version: ${{ env.BIOME_EXPECTED_VERSION }}" == "${{ steps.version.outputs.version }}" ]; then`. These should be passed via env: variables and referenced as quoted shell variables (e.g., "$STEP_OUTPUT") instead.

Locations:

- `.github/workflows/test.yaml:38`
- `.github/workflows/test.yaml:57`
- `.github/workflows/test.yaml:79`
- `.github/workflows/test.yaml:101`
- `.github/workflows/test.yaml:123`
- `.github/workflows/test.yaml:145`
- `.github/workflows/test.yaml:167`
- `.github/workflows/test.yaml:189`
- `.github/workflows/test.yaml:211`
- `.github/workflows/test.yaml:233`
- `.github/workflows/test.yaml:265`

### missing-permissions (severity: medium)

integrate.yaml has no top-level permissions: key and no job-level permissions: keys on any of its jobs (build, coding-standards). Without explicit permissions, the workflow inherits the repository default (often write-all for private repos), violating the principle of least privilege.

Locations:

- `.github/workflows/integrate.yaml:1`

### missing-permissions (severity: medium)

test.yaml has no top-level permissions: key and no job-level permissions: keys on any of its many jobs. Without explicit permissions, the workflow inherits the repository default, violating the principle of least privilege.

Locations:

- `.github/workflows/test.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed all three findings: (1) script-injection in .github/workflows/test.yaml - moved all ${{ steps.version.outputs.version }} and ${{ env.BIOME_EXPECTED_VERSION }} expressions from run: shell blocks into env: blocks (as STEP_VERSION and BIOME_EXPECTED_VERSION), referencing them as plain quoted shell variables ($STEP_VERSION, $BIOME_EXPECTED_VERSION) in the run: scripts across all 11 affected locations; (2) missing-permissions in .github/workflows/integrate.yaml - added top-level 'permissions: {}'; (3) missing-permissions in .github/workflows/test.yaml - added top-level 'permissions: {}'.

