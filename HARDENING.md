<!-- markdownlint-disable -->

# Hardening Report: oke-py--npm-audit-action/v4.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **oke-py--npm-audit-action/v4.0.1** was hardened automatically. 9 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'git tag' run: block in git-tag.yml directly interpolates ${{ github.event.release.tag_name }} into a shell command. This allows an attacker who can create a release with a crafted tag name to inject arbitrary shell commands. The offending line is: `MAJOR_VERSION=$(echo ${{ github.event.release.tag_name }} | grep -o "^v[0-9]*")`

Locations:

- `.github/workflows/git-tag.yml:22`

### unpinned-uses (severity: high)

All uses: references in daily.yml use mutable version tags instead of pinned SHA digests, making the workflow vulnerable to supply-chain attacks if the referenced action is compromised or its tag is moved. Failing references: actions/checkout@v6 (line 15), oke-py/npm-audit-action@v4 (line 17).

Locations:

- `.github/workflows/daily.yml:15`
- `.github/workflows/daily.yml:17`

### unpinned-uses (severity: high)

All uses: references in git-tag.yml use mutable version tags instead of pinned SHA digests. Failing references: actions/checkout@v6 (line 9).

Locations:

- `.github/workflows/git-tag.yml:9`

### unpinned-uses (severity: high)

All uses: references in licensed.yml use mutable version tags instead of pinned SHA digests. Failing references: actions/checkout@v6 (line 27), actions/setup-node@v6 (line 32), ruby/setup-ruby@v1 (line 39), licensee/setup-licensed@v1.3.2 (line 42).

Locations:

- `.github/workflows/licensed.yml:27`
- `.github/workflows/licensed.yml:32`
- `.github/workflows/licensed.yml:39`
- `.github/workflows/licensed.yml:42`

### unpinned-uses (severity: high)

All uses: references in test.yml use mutable version tags instead of pinned SHA digests. Failing references: actions/checkout@v6 (lines 14, 30, 46), actions/setup-node@v6 (lines 15, 31, 47), coverallsapp/github-action@master (line 22).

Locations:

- `.github/workflows/test.yml:14`
- `.github/workflows/test.yml:15`
- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:30`
- `.github/workflows/test.yml:31`
- `.github/workflows/test.yml:46`
- `.github/workflows/test.yml:47`

### unpinned-uses (severity: high)

All uses: references in update-dist.yml use mutable version tags instead of pinned SHA digests. Failing references: actions/checkout@v6 (line 24), actions/setup-node@v6 (line 29), stefanzweifel/git-auto-commit-action@v7 (line 57).

Locations:

- `.github/workflows/update-dist.yml:24`
- `.github/workflows/update-dist.yml:29`
- `.github/workflows/update-dist.yml:57`

### missing-permissions (severity: medium)

daily.yml has no top-level permissions: key and no job-level permissions: key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/daily.yml:1`

### missing-permissions (severity: medium)

git-tag.yml has no top-level permissions: key and no job-level permissions: key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/git-tag.yml:1`

### missing-permissions (severity: medium)

test.yml has no top-level permissions: key and no job-level permissions: key on any of its jobs. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 9 findings across 5 workflow files:

1. script-injection (git-tag.yml): Moved `${{ github.event.release.tag_name }}` into the step's env: block as TAG_NAME, then referenced it as "$TAG_NAME" in the shell script.

2. unpinned-uses: Pinned all action references to full commit SHAs:
   - actions/checkout@v6 → @df4cb1c069e1874edd31b4311f1884172cec0e10
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38
   - oke-py/npm-audit-action@v4 → @828ccb3b0710dfb351b6e9aaadec963c6953cacf
   - ruby/setup-ruby@v1 → @003a5c4d8d6321bd302e38f6f0ec593f77f06600
   - licensee/setup-licensed@v1.3.2 → @0d52e575b3258417672be0dff2f115d7db8771d8
   - coverallsapp/github-action@master → @09b709cf6a16e30b0808ba050c7a6e8a5ef13f8d
   - stefanzweifel/git-auto-commit-action@v7 → @4a55954c782fc1ea30b9056cd3e7a2b40ca8887d

3. missing-permissions: Added permissions blocks to daily.yml (contents:read, issues:write), git-tag.yml (contents:write), and test.yml (contents:read). licensed.yml and update-dist.yml already had permissions blocks.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed three unquoted shell variable expansions of `${MAJOR_VERSION}` in `.github/workflows/git-tag.yml`. The variable is derived from the attacker-controllable `github.event.release.tag_name` value. Changed `git push origin -d ${MAJOR_VERSION}`, `git tag ${MAJOR_VERSION} ${GITHUB_SHA}`, and `git push origin ${MAJOR_VERSION}` to use double-quoted form `"${MAJOR_VERSION}"` in all three places, preventing shell metacharacter injection.

