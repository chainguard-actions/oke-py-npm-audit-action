<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v3.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): GitHub Actions expressions are interpolated directly inside run: shell command strings, enabling script injection. In git-tag.yml line 21, `${{ github.event.release.tag_name }}` is embedded directly in a shell command: `MAJOR_VERSION=$(echo ${{ github.event.release.tag_name }} | grep -o "^v[0-9]*")`. An attacker who can create a release with a crafted tag name (e.g. containing shell metacharacters) can execute arbitrary commands. In package-version.yml lines 24 and 43, `${{ github.head_ref }}` is embedded directly in `git push origin HEAD:${{ github.head_ref }}` — a PR author controls the branch name and can inject shell commands.

Locations:

- `.github/workflows/git-tag.yml:21`
- `.github/workflows/package-version.yml:24`
- `.github/workflows/package-version.yml:43`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tag or branch refs instead of immutable 40-character commit SHA digests. This exposes the workflows to supply-chain attacks if any referenced action's tag is moved or the repository is compromised. Failing references include: daily.yml — `actions/checkout@v4`, `oke-py/npm-audit-action@v3`; dist.yml — `actions/checkout@v4`, `stefanzweifel/git-auto-commit-action@v5`; git-tag.yml — `actions/checkout@v4`; package-version.yml — `actions/checkout@v4`; test.yml — `actions/checkout@v4`, `actions/setup-node@v4`, `coverallsapp/github-action@master` (branch ref).

Locations:

- `.github/workflows/daily.yml:9`
- `.github/workflows/daily.yml:10`
- `.github/workflows/daily.yml:24`
- `.github/workflows/daily.yml:32`
- `.github/workflows/dist.yml:10`
- `.github/workflows/dist.yml:21`
- `.github/workflows/git-tag.yml:12`
- `.github/workflows/package-version.yml:13`
- `.github/workflows/package-version.yml:30`
- `.github/workflows/test.yml:16`
- `.github/workflows/test.yml:17`
- `.github/workflows/test.yml:24`

### missing-permissions (severity: medium)

None of the five workflow files define a top-level `permissions:` key, and no individual job within any of these files defines a `permissions:` key either. Without explicit permissions, workflows run with the default token permissions (which may be read-write depending on repository settings), granting broader access than necessary. All five workflow files are affected: daily.yml, dist.yml, git-tag.yml, package-version.yml, and test.yml.

Locations:

- `.github/workflows/daily.yml:1`
- `.github/workflows/dist.yml:1`
- `.github/workflows/git-tag.yml:1`
- `.github/workflows/package-version.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across five workflow files:

1. script-injection: In git-tag.yml, moved `${{ github.event.release.tag_name }}` into an env var `TAG_NAME` and used `echo "$TAG_NAME"` in the shell. In package-version.yml (both minor and patch jobs), moved `${{ github.head_ref }}` into env var `HEAD_REF` and used `"$HEAD_REF"` in the git push command.

2. unpinned-uses: Pinned all action references to full 40-char SHAs: actions/checkout@v4 → 11d5960a..., oke-py/npm-audit-action@v3 → 6ec7878c..., stefanzweifel/git-auto-commit-action@v5 → b863ae19..., actions/setup-node@v4 → 49933ea5..., coverallsapp/github-action@master → 09b709cf...

3. missing-permissions: Added top-level permissions blocks to all five files with minimal required permissions: daily.yml (contents: read, issues: write), dist.yml (contents: write), git-tag.yml (contents: write), package-version.yml (contents: write), test.yml (contents: read).

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/git-tag.yml by double-quoting all three unquoted expansions of ${MAJOR_VERSION} (lines 26, 29, 30). The variable was derived from the untrusted `github.event.release.tag_name` context value and expanded unquoted in `git push origin -d ${MAJOR_VERSION}`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}`. All three are now properly quoted: `"${MAJOR_VERSION}"` and `"${GITHUB_SHA}"`, preventing shell metacharacter injection.

