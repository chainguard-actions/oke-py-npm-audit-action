<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **oke-py--npm-audit-action/v5.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two uses of `oke-py/npm-audit-action@v5` in daily.yml reference a mutable version tag (`@v5`) rather than a full 40-character commit SHA. This means the action could be silently replaced with malicious code without any change to the workflow file. Both occurrences are in the `scan` and `scan-on-windows` jobs.

Locations:

- `.github/workflows/daily.yml:27`
- `.github/workflows/daily.yml:47`

### script-injection (severity: high)

Rule (b) violation in git-tag.yml: The shell variable `${MAJOR_VERSION}` — derived from `RELEASE_TAG` which is set from `${{ github.event.release.tag_name }}` — is used unquoted in three git commands: `git push origin -d ${MAJOR_VERSION}`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}`. An attacker who can create a release with a crafted tag name containing shell metacharacters (`;`, `|`, `&`, etc.) could inject arbitrary shell commands. The variable must be double-quoted: `"${MAJOR_VERSION}"`.

Locations:

- `.github/workflows/git-tag.yml:31`
- `.github/workflows/git-tag.yml:34`
- `.github/workflows/git-tag.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Pinned both `oke-py/npm-audit-action@v5` references (lines 27 and 47) to the full commit SHA `9401043799369ac417d1485c108bf5d327982b73` with `# v5` comment for readability. Removed the 'intentionally unpinned' comments and zizmor ignore suppressions. 2. git-tag.yml: Double-quoted `${MAJOR_VERSION}` in all three git commands (`git push origin -d`, `git tag`, and `git push origin`) to prevent shell metacharacter injection from attacker-controlled release tag names. Also double-quoted `${GITHUB_SHA}` for consistency.

