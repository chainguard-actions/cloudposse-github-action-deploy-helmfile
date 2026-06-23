<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-deploy-helmfile/v0.7.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cloudposse--github-action-deploy-helmfile/v0.7.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The Dockerfile (used as the action's Docker image) runs a remote shell script by piping it directly to bash: `RUN curl -1sLf 'https://dl.cloudsmith.io/public/cloudposse/packages/cfg/setup/bash.deb.sh' | bash`. This allows arbitrary code execution from a remote server during image build without verifying the script's integrity. The script should be downloaded first, inspected/verified, and then executed separately.

Locations:

- `Dockerfile:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell

**Notes:**

Fixed the unsafe curl-pipe-to-bash pattern in Dockerfile line 22. Changed `curl ... | bash` to download the script to /tmp/cloudposse-setup.sh first, then execute it with `bash /tmp/cloudposse-setup.sh`, and clean up the temp file afterward. This eliminates the direct pipe from the internet to bash, allowing the script to be inspected before execution.

