<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-deploy-helmfile/0.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cloudposse--github-action-deploy-helmfile/0.7.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of full 40-character commit SHAs, making them vulnerable to supply-chain attacks.

.github/workflows/test-positive.yml:
  - uses: actions/checkout@v4
  - uses: AbsaOSS/k3d-action@v2
  - uses: mikefarah/yq@v4.42.1
  - uses: dcarbone/install-jq-action@v1.0.1
  - uses: nick-fields/assert-action@v2

.github/workflows/_test-negative.yaml:
  - uses: actions/checkout@v4
  - uses: nick-fields/assert-action@v2

.github/workflows/feature-branch.yml:
  - uses: cloudposse/github-actions-workflows-github-action-composite/.github/workflows/feature-branch.yml@main

.github/workflows/main-branch.yaml:
  - uses: cloudposse/github-actions-workflows-github-action-composite/.github/workflows/main-branch.yml@main

.github/workflows/release.yml:
  - uses: cloudposse/github-actions-workflows-github-action-composite/.github/workflows/release.yml@main

Locations:

- `.github/workflows/test-positive.yml:29`
- `.github/workflows/test-positive.yml:43`
- `.github/workflows/test-positive.yml:57`
- `.github/workflows/test-positive.yml:63`
- `.github/workflows/test-positive.yml:113`
- `.github/workflows/_test-negative.yaml:21`
- `.github/workflows/_test-negative.yaml:33`
- `.github/workflows/feature-branch.yml:12`
- `.github/workflows/main-branch.yaml:13`
- `.github/workflows/release.yml:9`

### missing-permissions (severity: medium)

Two workflow files have no top-level `permissions:` key and no job-level `permissions:` blocks on any of their jobs. Without explicit permissions, workflows inherit the default (potentially broad) repository token permissions.

- .github/workflows/test-positive.yml: no top-level or job-level permissions defined across jobs: setup, test, assert, teardown.
- .github/workflows/_test-negative.yaml: no top-level or job-level permissions defined across jobs: setup, test, assert, teardown.

Locations:

- `.github/workflows/test-positive.yml:1`
- `.github/workflows/_test-negative.yaml:1`

### github-env-injection (severity: high)

In entrypoint.sh, the variable ENTRYPOINT is populated from Kubernetes ingress annotation data via kubectl and jq, then written directly to $GITHUB_OUTPUT without the required sanitization step (`printf '%s' "$ENTRYPOINT" | tr -d '\n\r'`). The value originates from external cluster data (Kubernetes ingress annotations) that could contain embedded newlines, allowing an attacker who controls annotation values to inject additional key=value pairs into the GitHub Actions output context.

Offending line:
  echo "webapp-url=${ENTRYPOINT}" >> $GITHUB_OUTPUT

Fix: sanitize before writing:
  safe=$(printf '%s' "${ENTRYPOINT}" | tr -d '\n\r')
  echo "webapp-url=${safe}" >> "$GITHUB_OUTPUT"

Locations:

- `entrypoint.sh:57`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, github-env-injection

**Notes:**

1. unpinned-uses: Pinned all action references to full commit SHAs: actions/checkout@v4 → 34e114876b0b11c390a56381ad16ebd13914f8d5, AbsaOSS/k3d-action@v2 → 4e8b3239042be1dc0aed6c5eb80c13b18200fc79, mikefarah/yq@v4.42.1 → 9adde1ac14bb283b8955d2b0d567bcaf3c69e639, dcarbone/install-jq-action@v1.0.1 → 91d8da7268538e8a0ae0c8b72af44f1763228455, nick-fields/assert-action@v2 → aa0067e01f0f6545c31755d6ca128c5a3a14f6bf, and cloudposse/github-actions-workflows-github-action-composite@main → a4d0078b26faab74b046f3104f39a40f5436aa92 (used in feature-branch.yml, main-branch.yaml, and release.yml). Original tags preserved as comments. 2. missing-permissions: Added `permissions: contents: read` top-level block to test-positive.yml and _test-negative.yaml. 3. github-env-injection: Fixed entrypoint.sh line 57 to sanitize the ENTRYPOINT value with `safe=$(printf '%s' "${ENTRYPOINT}" | tr -d '\n\r')` before writing `webapp-url=${safe}` to $GITHUB_OUTPUT, preventing newline injection attacks.

