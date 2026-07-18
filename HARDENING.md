<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v5.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

daily.yml references `oke-py/npm-audit-action@v5` — a mutable version tag rather than a pinned 40-character commit SHA. If the tag is moved (intentionally or by a supply-chain attacker), a different, potentially malicious version of the action will run. This appears twice in the file (once in the `scan` job and once in the `scan-on-windows` job). The comment `# zizmor: ignore[unpinned-uses]` suppresses the existing zizmor check but does not mitigate the actual risk.

Locations:

- `.github/workflows/daily.yml:22`
- `.github/workflows/daily.yml:40`

### script-injection (severity: high)

Rule (b) violation in git-tag.yml: the shell variable `${MAJOR_VERSION}` — derived from `RELEASE_TAG` which is set to `${{ github.event.release.tag_name }}` — is expanded unquoted in three shell commands: `git push origin -d ${MAJOR_VERSION} || true`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}`. An attacker who can publish a release with a crafted tag name containing shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) could inject arbitrary shell commands into the runner. All three expansions must be double-quoted: `"${MAJOR_VERSION}"`.

Locations:

- `.github/workflows/git-tag.yml:31`
- `.github/workflows/git-tag.yml:34`
- `.github/workflows/git-tag.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Replaced both occurrences of `oke-py/npm-audit-action@v5` with the pinned SHA `oke-py/npm-audit-action@8809ddd828be4c0bd281826b4dc61b50b1878fda # v5`. Removed the `# zizmor: ignore[unpinned-uses]` suppression comments since the actions are now properly pinned. 2. git-tag.yml: Double-quoted all three unquoted expansions of `${MAJOR_VERSION}` (in `git push origin -d`, `git tag`, and `git push origin`) and also double-quoted `${GITHUB_SHA}` in the `git tag` command, preventing shell injection from a crafted release tag name containing metacharacters.

