<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v5.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

daily.yml uses `oke-py/npm-audit-action@v5` — a mutable version tag rather than a pinned 40-character commit SHA. This means the action's code can change without notice, enabling supply-chain attacks. The reference appears in two steps (the `scan` job and the `scan-on-windows` job). All other `uses:` references in the repository are correctly pinned to full SHA digests.

Locations:

- `.github/workflows/daily.yml:21`
- `.github/workflows/daily.yml:40`

### script-injection (severity: high)

Rule (b) violation in git-tag.yml: the `git tag` run block expands several shell variables without double-quoting them. `RELEASE_TAG` is set from `${{ github.event.release.tag_name }}` (attacker-influenced via a crafted release name), and `MAJOR_VERSION` is derived from it via grep. The unquoted expansions include: `${GITHUB_REPOSITORY}` in the remote URL, `${MAJOR_VERSION}` in `git push origin -d ${MAJOR_VERSION}`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}`. An attacker who can publish a release with a crafted tag name containing shell metacharacters could inject arbitrary git commands.

Locations:

- `.github/workflows/git-tag.yml:23`
- `.github/workflows/git-tag.yml:26`
- `.github/workflows/git-tag.yml:30`
- `.github/workflows/git-tag.yml:33`
- `.github/workflows/git-tag.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Pinned both occurrences of `oke-py/npm-audit-action@v5` to the full commit SHA `8809ddd828be4c0bd281826b4dc61b50b1878fda` (resolved via lookup_action_sha). Removed the `zizmor: ignore[unpinned-uses]` suppression comments since the action is now properly pinned. 2. git-tag.yml: Double-quoted all unquoted shell variable expansions in the 'git tag' step: `${GITHUB_REPOSITORY}` in the remote URL, `${MAJOR_VERSION}` in `git push origin -d`, `git tag`, and `git push origin` commands, and `${GITHUB_SHA}` in the `git tag` command. The `RELEASE_TAG` env var (sourced from `github.event.release.tag_name`) was already in the env block; the fix ensures its derived value `MAJOR_VERSION` is always double-quoted when used in shell commands.

