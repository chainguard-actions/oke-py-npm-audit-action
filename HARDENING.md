<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v5.3.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses `oke-py/npm-audit-action@v5` (a mutable tag reference, not a full 40-character SHA commit pin) in two steps. A tag can be moved to point to a different, potentially malicious commit at any time, enabling a supply-chain attack. The `# zizmor: ignore[unpinned-uses]` comment suppresses the zizmor tool's warning but does not mitigate the underlying risk. Both occurrences should be replaced with a pinned SHA.

Locations:

- `.github/workflows/daily.yml:26`
- `.github/workflows/daily.yml:44`

### script-injection (severity: high)

Rule (b) violation: In the 'git tag' run block, the shell variable `${MAJOR_VERSION}` — derived from `RELEASE_TAG` which is set from `${{ github.event.release.tag_name }}` (a workflow-controllable value) — is expanded without double-quoting in three git commands: `git push origin -d ${MAJOR_VERSION}` (line 30), `git tag ${MAJOR_VERSION} ${GITHUB_SHA}` (line 33), and `git push origin ${MAJOR_VERSION}` (line 34). An attacker who can create a release with a crafted tag name containing shell metacharacters (`;`, `|`, `&`, etc.) could inject arbitrary shell commands. All three expansions must be quoted: `"${MAJOR_VERSION}"`.

Locations:

- `.github/workflows/git-tag.yml:30`
- `.github/workflows/git-tag.yml:33`
- `.github/workflows/git-tag.yml:34`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Replaced both `oke-py/npm-audit-action@v5` (mutable tag) references with the pinned SHA `oke-py/npm-audit-action@8809ddd828be4c0bd281826b4dc61b50b1878fda # v5`. The `# zizmor: ignore[unpinned-uses]` suppression comments were removed since the references are now properly pinned. 2. git-tag.yml: Double-quoted all three unquoted `${MAJOR_VERSION}` expansions (`git push origin -d`, `git tag`, and `git push origin`) to prevent shell injection from a crafted release tag name. Also double-quoted `${GITHUB_SHA}` in the `git tag` command for consistency.

