<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **oke-py--npm-audit-action/v5.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `oke-py/npm-audit-action@v4` — a mutable tag reference rather than a pinned 40-character commit SHA. This means the action could be silently replaced with a different (potentially malicious) version without any change to the workflow file. The comment `# zizmor: ignore[unpinned-uses]` suppresses the zizmor tool's warning but does not mitigate the actual supply-chain risk.

Locations:

- `.github/workflows/daily.yml:26`

### script-injection (severity: high)

Rule (b) violation: In the 'git tag' step, the shell variable `${MAJOR_VERSION}` — derived from `RELEASE_TAG` which holds `${{ github.event.release.tag_name }}` (a workflow-controllable value) — is used unquoted in three git commands: `git push origin -d ${MAJOR_VERSION}`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}`. Although MAJOR_VERSION is filtered through `grep -o "^v[0-9]*"`, the variable is still unquoted, allowing shell metacharacter injection if the grep produces unexpected output. All expansions of env vars holding workflow-controllable data must be double-quoted.

Locations:

- `.github/workflows/git-tag.yml:31`
- `.github/workflows/git-tag.yml:34`
- `.github/workflows/git-tag.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Replaced mutable tag `oke-py/npm-audit-action@v4` with pinned SHA `oke-py/npm-audit-action@828ccb3b0710dfb351b6e9aaadec963c6953cacf # v4`, removing the zizmor ignore comment. 2. git-tag.yml: Double-quoted all three expansions of `${MAJOR_VERSION}` in the 'git tag' step (`git push origin -d "${MAJOR_VERSION}"`, `git tag "${MAJOR_VERSION}" "${GITHUB_SHA}"`, `git push origin "${MAJOR_VERSION}"`) to prevent shell metacharacter injection from the workflow-controllable `github.event.release.tag_name` value.

