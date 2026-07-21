<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v5.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `oke-py/npm-audit-action@v4`, a mutable tag reference rather than a pinned 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, enabling a supply-chain attack. All other `uses:` references in this repository are properly pinned to full SHAs. The comment notes this is intentional for dogfooding, but it still represents a real supply-chain risk.

Locations:

- `.github/workflows/daily.yml:26`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned `oke-py/npm-audit-action@v4` to full commit SHA `828ccb3b0710dfb351b6e9aaadec963c6953cacf` in `.github/workflows/daily.yml` (line 26). The `# v4` tag is preserved as a comment for readability. The commented-out Windows job section also references `@v4` but is entirely commented out and not executed, so it was left as-is.

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all unquoted shell variable expansions in .github/workflows/git-tag.yml 'git tag' step: (1) double-quoted `${GITHUB_REPOSITORY}` in the remote URL string, (2) double-quoted `${MAJOR_VERSION}` in `git push origin -d`, (3) double-quoted both `${MAJOR_VERSION}` and `${GITHUB_SHA}` in `git tag`, and (4) double-quoted `${MAJOR_VERSION}` in the final `git push origin`. This prevents shell word-splitting and glob expansion on workflow-controllable values.

