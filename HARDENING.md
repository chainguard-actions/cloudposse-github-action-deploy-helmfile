<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-deploy-helmfile/v0.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-deploy-helmfile/v0.8.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in test-positive.yml are pinned to mutable tags instead of full 40-character SHA digests, making the workflow vulnerable to supply-chain attacks if the referenced tag is moved or overwritten: `actions/checkout@v4` (line 29), `AbsaOSS/k3d-action@v2` (line 38), `mikefarah/yq@v4.42.1` (line 55), `dcarbone/install-jq-action@v1.0.1` (line 60), `nick-fields/assert-action@v2` (line 107).

Locations:

- `.github/workflows/test-positive.yml:29`
- `.github/workflows/test-positive.yml:38`
- `.github/workflows/test-positive.yml:55`
- `.github/workflows/test-positive.yml:60`
- `.github/workflows/test-positive.yml:107`

### unpinned-uses (severity: high)

Multiple `uses:` references in _test-negative.yaml are pinned to mutable tags instead of full 40-character SHA digests: `actions/checkout@v4` (line 23), `nick-fields/assert-action@v2` (line 35).

Locations:

- `.github/workflows/_test-negative.yaml:23`
- `.github/workflows/_test-negative.yaml:35`

### unpinned-uses (severity: high)

The `uses:` reference in branch.yml points to a mutable branch ref (`@main`) instead of a full 40-character SHA digest: `cloudposse/.github/.github/workflows/shared-github-action.yml@main`. A branch ref can be updated at any time, enabling supply-chain attacks.

Locations:

- `.github/workflows/branch.yml:19`

### unpinned-uses (severity: high)

The `uses:` reference in release.yml points to a mutable branch ref (`@main`) instead of a full 40-character SHA digest: `cloudposse/.github/.github/workflows/shared-release-branches.yml@main`. A branch ref can be updated at any time, enabling supply-chain attacks.

Locations:

- `.github/workflows/release.yml:9`

### missing-permissions (severity: medium)

The workflow file test-positive.yml has no top-level `permissions:` key and none of its jobs (setup, test, assert, teardown) define job-level `permissions:` blocks. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/test-positive.yml:1`

### missing-permissions (severity: medium)

The workflow file _test-negative.yaml has no top-level `permissions:` key and none of its jobs (setup, test, assert, teardown) define job-level `permissions:` blocks. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

Locations:

- `.github/workflows/_test-negative.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned `uses:` references by resolving mutable tags/branches to full 40-character SHA digests with tag comments preserved. Specifically: (1) test-positive.yml: pinned actions/checkout@v4→34e114876b0b11c390a56381ad16ebd13914f8d5, AbsaOSS/k3d-action@v2→4e8b3239042be1dc0aed6c5eb80c13b18200fc79, mikefarah/yq@v4.42.1→9adde1ac14bb283b8955d2b0d567bcaf3c69e639, dcarbone/install-jq-action@v1.0.1→91d8da7268538e8a0ae0c8b72af44f1763228455, nick-fields/assert-action@v2→aa0067e01f0f6545c31755d6ca128c5a3a14f6bf; added top-level `permissions: contents: read`. (2) _test-negative.yaml: pinned actions/checkout@v4 and nick-fields/assert-action@v2 to same SHAs; added top-level `permissions: contents: read`. (3) branch.yml: pinned cloudposse/.github shared-github-action.yml@main→8244c7c9142e92281e7841f655fa48e9ceb9b454. (4) release.yml: pinned cloudposse/.github shared-release-branches.yml@main→8244c7c9142e92281e7841f655fa48e9ceb9b454.

