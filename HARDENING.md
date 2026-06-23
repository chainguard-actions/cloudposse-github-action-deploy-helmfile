<!-- markdownlint-disable -->

# Hardening Report: cloudposse--github-action-deploy-helmfile/v0.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cloudposse--github-action-deploy-helmfile/v0.8.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unsafe-shell (severity: high)

The Dockerfile pipes remote content directly to bash: `curl -1sLf 'https://dl.cloudsmith.io/public/cloudposse/packages/cfg/setup/bash.deb.sh' | bash`. This executes arbitrary remote code without first downloading and verifying the script, which is a supply-chain risk.

Locations:

- `Dockerfile:22`

## Iteration Notes

### Iteration 1

**Fixes applied:** unsafe-shell

**Notes:**

Fixed the unsafe curl | bash pattern in Dockerfile line 22. The remote setup script is now downloaded to /tmp/cloudposse-setup.sh first, then executed separately with bash, and cleaned up afterward. This eliminates the supply-chain risk of piping remote content directly to a shell interpreter.

