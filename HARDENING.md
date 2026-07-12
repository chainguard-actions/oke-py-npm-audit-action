<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **oke-py--npm-audit-action/v5.3.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses 'oke-py/npm-audit-action@v5' (a mutable tag reference, not a full 40-character commit SHA) in two steps. A supply-chain attacker who gains write access to that repository could push a malicious version under the same tag without any change to this workflow file. The comment 'intentionally unpinned' does not mitigate the risk. Failing references: line 26 (scan job) and line 44 (scan-on-windows job).

Locations:

- `.github/workflows/daily.yml:26`
- `.github/workflows/daily.yml:44`

### script-injection (severity: high)

Sub-rule (b): The 'git tag' run block expands ${MAJOR_VERSION} unquoted in three shell commands. MAJOR_VERSION is computed from RELEASE_TAG, which is set to ${{ github.event.release.tag_name }} in the env: block. A release tag name containing shell metacharacters (e.g. spaces, semicolons, backticks) would be split or interpreted by the shell before git sees it. Offending lines: 'git push origin -d ${MAJOR_VERSION} || true' (line 31), 'git tag ${MAJOR_VERSION} ${GITHUB_SHA}' (line 34), 'git push origin ${MAJOR_VERSION}' (line 35). Fix: quote all expansions as "${MAJOR_VERSION}".

Locations:

- `.github/workflows/git-tag.yml:31`
- `.github/workflows/git-tag.yml:34`
- `.github/workflows/git-tag.yml:35`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Pinned both occurrences of `oke-py/npm-audit-action@v5` to full commit SHA `96715dda926adae1af5001117edfd47b2a68ed02 # v5` (lines 26 and 44). Removed the 'intentionally unpinned' comments and zizmor ignore annotations since the action is now properly pinned. 2. git-tag.yml: Quoted all three unquoted `${MAJOR_VERSION}` shell expansions (git push origin -d, git tag, git push origin) with double quotes to prevent shell metacharacter injection from attacker-controlled release tag names. Also quoted `${GITHUB_SHA}` in the git tag command for consistency.

