<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **oke-py--npm-audit-action/v5.2.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

daily.yml uses 'oke-py/npm-audit-action@v5' (a mutable tag, not a full 40-character SHA) in two steps. This means the action could be silently replaced with a malicious version without any change to the workflow file. Both occurrences are in the scan and scan-on-windows jobs.

Locations:

- `.github/workflows/daily.yml:26`
- `.github/workflows/daily.yml:43`

### script-injection (severity: high)

git-tag.yml 'git tag' run block (sub-rule b): the env var MAJOR_VERSION is derived from RELEASE_TAG (${{ github.event.release.tag_name }}) and is used unquoted in three shell commands: 'git push origin -d ${MAJOR_VERSION} || true' (line 31), 'git tag ${MAJOR_VERSION} ${GITHUB_SHA}' (line 34), and 'git push origin ${MAJOR_VERSION}' (line 35). An attacker who can control the release tag name could inject shell metacharacters. Additionally, ${GITHUB_REPOSITORY} is used unquoted in the remote URL on line 25.

Locations:

- `.github/workflows/git-tag.yml:25`
- `.github/workflows/git-tag.yml:31`
- `.github/workflows/git-tag.yml:34`
- `.github/workflows/git-tag.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Replaced both `oke-py/npm-audit-action@v5` references (lines 26 and 43) with the pinned SHA `oke-py/npm-audit-action@96715dda926adae1af5001117edfd47b2a68ed02 # v5`. Removed the intentional-unpinning comments that were suppressing the finding.
2. git-tag.yml: Fixed script injection by double-quoting all unquoted variable expansions in the 'git tag' run block: `${GITHUB_REPOSITORY}` in the remote URL (line 25), and `${MAJOR_VERSION}` in the three git commands on lines 31, 34, and 35. The `RELEASE_TAG` (from `github.event.release.tag_name`) was already safely passed via the env block.

