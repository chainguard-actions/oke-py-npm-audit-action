<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.4.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v5.4.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation in .github/workflows/git-tag.yml: The env var RELEASE_TAG is set from the untrusted expression `${{ github.event.release.tag_name }}` and used to derive MAJOR_VERSION, which is then expanded **unquoted** in multiple shell commands: `git push origin -d ${MAJOR_VERSION}`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}`. An attacker who can create a release with a crafted tag name containing shell metacharacters (`;`, `|`, `&`, etc.) could achieve command injection. All expansions of MAJOR_VERSION must be double-quoted.

Locations:

- `.github/workflows/git-tag.yml:24`
- `.github/workflows/git-tag.yml:32`
- `.github/workflows/git-tag.yml:35`
- `.github/workflows/git-tag.yml:36`

### unpinned-uses (severity: high)

Two steps in .github/workflows/daily.yml reference `oke-py/npm-audit-action@v5` using a mutable version tag instead of a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack. Each reference should be pinned to a specific SHA (e.g., `oke-py/npm-audit-action@<40-hex-sha> # v5`). Note: the workflow comments acknowledge this is intentional dogfooding, but it still constitutes an unpinned reference per security policy.

Locations:

- `.github/workflows/daily.yml:28`
- `.github/workflows/daily.yml:48`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

1. script-injection (.github/workflows/git-tag.yml): Double-quoted all three unquoted expansions of MAJOR_VERSION in the 'git tag' step: `git push origin -d "${MAJOR_VERSION}"`, `git tag "${MAJOR_VERSION}" "${GITHUB_SHA}"`, and `git push origin "${MAJOR_VERSION}"`. This prevents shell metacharacters in a crafted release tag name from achieving command injection. 2. unpinned-uses (.github/workflows/daily.yml): Pinned both occurrences of `oke-py/npm-audit-action@v5` to the full commit SHA `4fa25b0230596f76d96b7e50b66b1e3a0d185d9f` with `# v5` comment preserved for readability.

