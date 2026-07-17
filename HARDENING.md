<!-- markdownlint-disable -->

# Hardening Report: biomejs--setup-biome/v2.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **biomejs--setup-biome/v2.7.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in test.yaml directly interpolate `${{ steps.version.outputs.version }}` and `${{ env.BIOME_EXPECTED_VERSION }}` inside shell command strings. Every 'Check equality' step across all test jobs contains patterns like: `if [ "Version: 2.0.0" == "${{ steps.version.outputs.version }}" ]` and `if [ "Version: ${{ env.BIOME_EXPECTED_VERSION }}" == "${{ steps.version.outputs.version }}" ]`. Any `${{ ... }}` expression interpolated directly into a `run:` block is a script-injection risk because the value is substituted by the Actions runner before the shell ever sees it, allowing metacharacter injection.

Locations:

- `.github/workflows/test.yaml:38`
- `.github/workflows/test.yaml:71`
- `.github/workflows/test.yaml:104`
- `.github/workflows/test.yaml:137`
- `.github/workflows/test.yaml:170`
- `.github/workflows/test.yaml:203`
- `.github/workflows/test.yaml:236`
- `.github/workflows/test.yaml:269`
- `.github/workflows/test.yaml:302`
- `.github/workflows/test.yaml:335`
- `.github/workflows/test.yaml:368`
- `.github/workflows/test.yaml:401`
- `.github/workflows/test.yaml:434`

### script-injection (severity: high)

Sub-rule (a): The 'Auto-merge Pull Request' step directly interpolates `${{ steps.pr.outputs.pull-request-number }}` inside a `run:` shell command: `run: gh pr merge --squash --auto "${{ steps.pr.outputs.pull-request-number }}"`  The step output value is substituted by the Actions runner before the shell executes, allowing an attacker who can influence the PR number output to inject shell metacharacters. The value should be passed via an `env:` variable and referenced as a quoted shell variable instead.

Locations:

- `.github/workflows/update-readme-package-version.yaml:45`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., write access to contents). A `permissions: {}` or minimal scoped block should be added at the top level or per job.

Locations:

- `.github/workflows/integrate.yaml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad. A `permissions: {}` or minimal scoped block should be added at the top level or per job.

Locations:

- `.github/workflows/test.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions

**Notes:**

Fixed all 4 findings across 3 workflow files:

1. test.yaml (script-injection + missing-permissions): Moved all 13 instances of `${{ steps.version.outputs.version }}` and `${{ env.BIOME_EXPECTED_VERSION }}` from `run:` blocks into `env:` blocks (as `ACTUAL_VERSION` and `BIOME_EXPECTED_VERSION`), referencing them as plain shell variables in the scripts. Added `permissions: {}` at the top level.

2. update-readme-package-version.yaml (script-injection): Moved `${{ steps.pr.outputs.pull-request-number }}` from the `run:` command into an `env:` block as `PR_NUMBER`, referenced as `"$PR_NUMBER"` in the shell command.

3. integrate.yaml (missing-permissions): Added `permissions: {}` at the top level since the workflow only performs read-only operations (checkout, install, build, lint).

