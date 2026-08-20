<!-- markdownlint-disable -->

# Hardening Report: docker--setup-docker-action/v5.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **docker--setup-docker-action/v5.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of pinned full-length SHA commit hashes. This exposes the workflow to supply-chain attacks where a tag can be silently moved to point to malicious code.

ci.yml: actions/checkout@v6 (×13 occurrences), docker/setup-buildx-action@v3 (×2), docker/build-push-action@v6 (×2)
publish.yml: actions/checkout@v6, actions/publish-immutable-action@v0.0.4
test.yml: actions/checkout@v6, docker/bake-action@v6, codecov/codecov-action@v5
update-dist.yml: actions/create-github-app-token@v2, actions/checkout@v6, docker/bake-action@v6
validate.yml: actions/checkout@v6, docker/bake-action@v6, docker/bake-action/subaction/list-targets@v6

All of these should be replaced with pinned SHA references, e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.

Locations:

- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:55`
- `.github/workflows/ci.yml:75`
- `.github/workflows/ci.yml:100`
- `.github/workflows/ci.yml:119`
- `.github/workflows/ci.yml:140`
- `.github/workflows/ci.yml:162`
- `.github/workflows/ci.yml:167`
- `.github/workflows/ci.yml:172`
- `.github/workflows/publish.yml:14`
- `.github/workflows/publish.yml:17`
- `.github/workflows/test.yml:15`
- `.github/workflows/test.yml:18`
- `.github/workflows/test.yml:22`
- `.github/workflows/update-dist.yml:14`
- `.github/workflows/update-dist.yml:22`
- `.github/workflows/update-dist.yml:30`
- `.github/workflows/validate.yml:17`
- `.github/workflows/validate.yml:20`
- `.github/workflows/validate.yml:37`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (which may be read/write), granting more access than necessary and violating the principle of least privilege.

- ci.yml: no permissions declared at top level or on any of its many jobs (main, channel, daemon-config, context, lima-start-args, build-linux, build-macos, build-windows, set-host, rootless, tcp, undock-regctl-version, docker-29)
- test.yml: no permissions declared at top level or on the 'test' job
- update-dist.yml: no permissions declared at top level or on the 'update-dist' job (this job also checks out a PR head ref and pushes commits, making the missing permissions especially risky)
- validate.yml: no permissions declared at top level or on the 'prepare'/'validate' jobs

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/test.yml:1`
- `.github/workflows/update-dist.yml:1`
- `.github/workflows/validate.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 5 workflow files:

**unpinned-uses**: Pinned all action references to full SHA commit hashes:
- actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6 (13 occurrences in ci.yml + 1 each in publish.yml, test.yml, update-dist.yml, validate.yml)
- docker/setup-buildx-action@v3 → @8d2750c68a42422c14e847fe6c8ac0403b4cbd6f # v3 (2 occurrences in ci.yml)
- docker/build-push-action@v6 → @10e90e3645eae34f1e60eeb005ba3a3d33f178e8 # v6 (2 occurrences in ci.yml)
- actions/publish-immutable-action@v0.0.4 → @4bc8754ffc40f27910afb20287dbbbb675a4e978 # v0.0.4 (publish.yml)
- docker/bake-action@v6 → @5be5f02ff8819ecd3092ea6b2e6261c31774f2b4 # v6 (test.yml, update-dist.yml, validate.yml)
- codecov/codecov-action@v5 → @0fb7174895f61a3b6b78fc075e0cd60383518dac # v5 (test.yml)
- actions/create-github-app-token@v2 → @fee1f7d63c2ff003460e3d139729b119787bc349 # v2 (update-dist.yml)
- docker/bake-action/subaction/list-targets@v6 → @5be5f02ff8819ecd3092ea6b2e6261c31774f2b4 # v6 (validate.yml)

**missing-permissions**: Added explicit permissions blocks to all jobs:
- ci.yml: Added `permissions: contents: read` to all 13 jobs (main, channel, daemon-config, context, lima-start-args, build-linux, build-macos, build-windows, set-host, rootless, tcp, undock-regctl-version, docker-29)
- test.yml: Added `permissions: contents: read` to the test job
- update-dist.yml: Added `permissions: contents: write` to the update-dist job (needs write to push commits)
- validate.yml: Added `permissions: contents: read` to both prepare and validate jobs
- publish.yml: Already had appropriate permissions (contents: read, id-token: write, packages: write)

