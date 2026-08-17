<!-- markdownlint-disable -->

# Hardening Report: crazy-max--ghaction-upx/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **crazy-max--ghaction-upx/v4.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the tag is moved.

Failing references:
- ci.yml: `actions/checkout@v4` (appears twice)
- test.yml: `actions/checkout@v4`, `docker/bake-action@v6`, `codecov/codecov-action@v5`
- validate.yml: `actions/checkout@v4`, `docker/bake-action/subaction/list-targets@v6`, `docker/bake-action@v6`
- labels.yml: `actions/checkout@v4`, `crazy-max/ghaction-github-labeler@v5`
- upx-releases-json.yml: `actions/checkout@v4`, `actions/download-artifact@v4`

Locations:

- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:68`
- `.github/workflows/test.yml:22`
- `.github/workflows/test.yml:25`
- `.github/workflows/test.yml:28`
- `.github/workflows/validate.yml:22`
- `.github/workflows/validate.yml:26`
- `.github/workflows/validate.yml:40`
- `.github/workflows/labels.yml:22`
- `.github/workflows/labels.yml:25`
- `.github/workflows/upx-releases-json.yml:28`
- `.github/workflows/upx-releases-json.yml:32`

### script-injection (severity: high)

Sub-rule (a) violation: A GitHub Actions expression `${{ matrix.os }}` is interpolated directly inside a `run:` shell command string. Although `matrix.os` is workflow-controlled rather than directly attacker-supplied via a PR, any `${{ ... }}` inside a `run:` block undergoes YAML template substitution before the shell sees it, bypassing shell quoting. The offending line is:
  `if [ "${{ matrix.os }}" = "windows-latest" ]; then`
This should be replaced with an environment variable reference, e.g. `env: MATRIX_OS: ${{ matrix.os }}` and then `if [ "$MATRIX_OS" = "windows-latest" ]; then`.

Locations:

- `.github/workflows/ci.yml:41`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 12 unpinned action references across 5 workflow files by pinning to full SHA hashes (with tag comments): actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, docker/bake-action@v6 → @5be5f02ff8819ecd3092ea6b2e6261c31774f2b4, codecov/codecov-action@v5 → @0fb7174895f61a3b6b78fc075e0cd60383518dac, crazy-max/ghaction-github-labeler@v5 → @24d110aa46a59976b8a7f35518cb7f14f434c916, actions/download-artifact@v4 → @d3f86a106a0bac45b974a628896c90dbdf5c8093. Also fixed script-injection in ci.yml by moving `${{ matrix.os }}` out of the run block into an env variable `MATRIX_OS` and referencing it as `$MATRIX_OS` in the shell script.

