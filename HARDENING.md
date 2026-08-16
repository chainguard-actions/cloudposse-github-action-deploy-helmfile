<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-deploy-helmfile/v0.7.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-deploy-helmfile/v0.7.1** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions and reusable workflows using mutable tags or branch names instead of full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced tag or branch is updated with malicious code.

Failing references:
- branch.yml: `uses: cloudposse/.github/.github/workflows/shared-github-action.yml@main`
- release.yml: `uses: cloudposse/.github/.github/workflows/shared-release-branches.yml@main`
- test-positive.yml: `uses: actions/checkout@v4`, `uses: AbsaOSS/k3d-action@v2`, `uses: mikefarah/yq@v4.42.1`, `uses: dcarbone/install-jq-action@v1.0.1`, `uses: nick-fields/assert-action@v2`
- _test-negative.yaml: `uses: actions/checkout@v4`, `uses: nick-fields/assert-action@v2`

Locations:

- `.github/workflows/branch.yml:23`
- `.github/workflows/release.yml:13`
- `.github/workflows/test-positive.yml:22`
- `.github/workflows/test-positive.yml:30`
- `.github/workflows/test-positive.yml:43`
- `.github/workflows/test-positive.yml:48`
- `.github/workflows/test-positive.yml:86`
- `.github/workflows/_test-negative.yaml:21`
- `.github/workflows/_test-negative.yaml:33`

### missing-permissions (severity: medium)

The workflow files test-positive.yml and _test-negative.yaml have no top-level `permissions:` key and no job-level `permissions:` block on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions, which may be overly broad (e.g., write access to contents). Each workflow should declare minimal required permissions.

Locations:

- `.github/workflows/test-positive.yml:1`
- `.github/workflows/_test-negative.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by replacing mutable tags/branches with full 40-character commit SHAs (preserving original tags in comments). Added top-level `permissions: contents: read` blocks to test-positive.yml and _test-negative.yaml. All four workflow files were updated: branch.yml, release.yml, test-positive.yml, and _test-negative.yaml.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed entrypoint.sh line 47: The ENTRYPOINT value (derived from kubectl/jq output of Kubernetes ingress annotations, influenced by workflow-controlled inputs NAMESPACE, RELEASE_LABEL_NAME, RELEASE) is now sanitized before being written to $GITHUB_OUTPUT. Added `safe_entrypoint=$(printf '%s' "${ENTRYPOINT}" | tr -d '\n\r')` and changed the echo to use `safe_entrypoint` instead of `ENTRYPOINT` directly. Also quoted `"$GITHUB_OUTPUT"` for good measure.

