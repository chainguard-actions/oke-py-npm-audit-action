<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v4.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v4.0.2** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is directly interpolated inside a `run:` shell command string. In `.github/workflows/git-tag.yml` line 21, `${{ github.event.release.tag_name }}` is substituted directly into the shell command `MAJOR_VERSION=$(echo ${{ github.event.release.tag_name }} | grep -o "^v[0-9]*")`. An attacker who can publish a release with a crafted tag name (e.g., containing shell metacharacters) could achieve arbitrary command execution on the runner.

Locations:

- `.github/workflows/git-tag.yml:21`

### unpinned-uses (severity: high)

All workflow files reference external actions using mutable tags or branch names instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the referenced tag is moved or the action is compromised.

- daily.yml: `actions/checkout@v6`, `oke-py/npm-audit-action@v4` (appears twice in commented and active sections)
- git-tag.yml: `actions/checkout@v6`
- licensed.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `ruby/setup-ruby@v1`, `licensee/setup-licensed@v1.3.2`
- test.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `coverallsapp/github-action@master`
- update-dist.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `stefanzweifel/git-auto-commit-action@v7`

Locations:

- `.github/workflows/daily.yml:13`
- `.github/workflows/git-tag.yml:12`
- `.github/workflows/licensed.yml:27`
- `.github/workflows/test.yml:14`
- `.github/workflows/update-dist.yml:24`

### missing-permissions (severity: medium)

Three workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, workflows run with the repository's default token permissions, which may be overly broad (e.g., `write` access to contents). Each workflow should declare the minimal permissions required.

- `.github/workflows/daily.yml`: no permissions declared; the `scan` job calls `oke-py/npm-audit-action` which creates issues and needs `issues: write`, but the token scope is unconstrained.
- `.github/workflows/git-tag.yml`: no permissions declared; the `tag-major-version` job pushes tags and needs `contents: write`, but the full default token scope is granted.
- `.github/workflows/test.yml`: no permissions declared across `build`, `build-on-windows`, and `test` jobs.

Locations:

- `.github/workflows/daily.yml:1`
- `.github/workflows/git-tag.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across 5 workflow files: (1) script-injection in git-tag.yml line 21 — moved github.event.release.tag_name into env block as TAG_NAME and referenced as "$TAG_NAME" in shell; (2) unpinned-uses — pinned all 7 distinct action references to full 40-char SHAs with tag comments across daily.yml, git-tag.yml, licensed.yml, test.yml, and update-dist.yml; (3) missing-permissions — added top-level permissions blocks to daily.yml (issues: write), git-tag.yml (contents: write), and test.yml (contents: read).

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed three unquoted `${MAJOR_VERSION}` expansions in `.github/workflows/git-tag.yml` (lines 31, 34, 35) by adding double quotes around each occurrence. The variable is derived from the attacker-controlled `github.event.release.tag_name` release tag, and unquoted expansion allowed shell metacharacters (`;`, `|`, `&`, whitespace, globs) to be interpreted as shell commands. All three commands now use `"${MAJOR_VERSION}"`: `git push origin -d "${MAJOR_VERSION}" || true`, `git tag "${MAJOR_VERSION}" ${GITHUB_SHA}`, and `git push origin "${MAJOR_VERSION}"`.

