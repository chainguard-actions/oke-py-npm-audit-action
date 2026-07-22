<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v5.2.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `oke-py/npm-audit-action@v5` — a mutable tag reference rather than a pinned 40-character commit SHA. This means the action could be silently replaced with a different (potentially malicious) version without any change to the workflow file. This appears twice (in the `scan` and `scan-on-windows` jobs). The comment `# zizmor: ignore[unpinned-uses]` suppresses the zizmor tool's warning but does not eliminate the actual supply-chain risk.

Locations:

- `.github/workflows/daily.yml:26`
- `.github/workflows/daily.yml:41`

### script-injection (severity: high)

Rule (b) violation: In the `git tag` step, the env var `RELEASE_TAG` is set from `${{ github.event.release.tag_name }}` (a workflow-controlled value). It is used to compute `MAJOR_VERSION=$(echo "${RELEASE_TAG}" | grep -o "^v[0-9]*")`, and then `${MAJOR_VERSION}` is expanded **unquoted** in multiple shell commands: `git push origin -d ${MAJOR_VERSION} || true` (line 30), `git tag ${MAJOR_VERSION} ${GITHUB_SHA}` (line 33), and `git push origin ${MAJOR_VERSION}` (line 34). An attacker who can create a release with a crafted tag name could inject shell metacharacters. Additionally, `${GITHUB_REPOSITORY}` is unquoted in the remote URL on line 25: `https://github-actions:${GITHUB_TOKEN}@github.com/${GITHUB_REPOSITORY}`. All of these should be double-quoted: `"${MAJOR_VERSION}"`, `"${GITHUB_REPOSITORY}"`, etc.

Locations:

- `.github/workflows/git-tag.yml:25`
- `.github/workflows/git-tag.yml:30`
- `.github/workflows/git-tag.yml:33`
- `.github/workflows/git-tag.yml:34`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Pinned both occurrences of `oke-py/npm-audit-action@v5` to full SHA `8809ddd828be4c0bd281826b4dc61b50b1878fda # v5`. Removed the misleading 'intentionally unpinned' comments and zizmor suppression annotations. 2. git-tag.yml: Double-quoted all unquoted shell variable expansions in the 'git tag' step: `${GITHUB_REPOSITORY}` in the remote URL (line 25), and `${MAJOR_VERSION}` in `git push origin -d` (line 30), `git tag` (line 33), and `git push origin` (line 34). This prevents shell injection from attacker-controlled release tag names.

