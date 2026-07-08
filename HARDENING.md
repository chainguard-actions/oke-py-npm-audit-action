<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v5.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **oke-py--npm-audit-action/v5.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses oke-py/npm-audit-action@v5 (a mutable version tag, not a full 40-character SHA commit hash) in two jobs. This means the action could be silently replaced with a malicious version without any change to the workflow file. Both occurrences are in daily.yml.

Locations:

- `.github/workflows/daily.yml:27`
- `.github/workflows/daily.yml:43`

### script-injection (severity: high)

Rule (b) violation in git-tag.yml: The env var MAJOR_VERSION (derived from RELEASE_TAG, which is set from github.event.release.tag_name — a workflow-controllable value) is expanded unquoted in multiple shell commands: `git push origin -d ${MAJOR_VERSION} || true`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}`. An attacker who can publish a release with a crafted tag name could inject shell metacharacters. All shell expansions of this untrusted-derived variable must be double-quoted.

Locations:

- `.github/workflows/git-tag.yml:30`
- `.github/workflows/git-tag.yml:33`
- `.github/workflows/git-tag.yml:34`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. daily.yml: Pinned both occurrences of `oke-py/npm-audit-action@v5` to the full commit SHA `41a983db466d27a887bb5e6830abc0bb925cf9e3` (resolved via lookup_action_sha), preserving `# v5` as a comment for readability. Removed the `zizmor: ignore[unpinned-uses]` suppression comments since the actions are now properly pinned.
2. git-tag.yml: Double-quoted all three unquoted expansions of `${MAJOR_VERSION}` in the git tag step (`git push origin -d`, `git tag`, and `git push origin`) to prevent shell injection from attacker-controlled release tag names. The `RELEASE_TAG` env var already correctly maps the `github.event.release.tag_name` expression out of the shell script.

