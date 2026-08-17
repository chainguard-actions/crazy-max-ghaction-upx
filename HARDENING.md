<!-- markdownlint-disable -->

# Hardening Report: crazy-max--ghaction-upx/v3.2.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **crazy-max--ghaction-upx/v3.2.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression `${{ matrix.os }}` is directly interpolated inside a `run:` shell command string in ci.yml. Even though `matrix.os` is defined in the workflow's own matrix, it still flows through YAML template substitution before the shell processes it, making it a script-injection risk. The offending line is: `if [ "${{ matrix.os }}" = "windows-latest" ]; then`. It should be replaced with an env var reference, e.g. set `env: MATRIX_OS: ${{ matrix.os }}` and use `"$MATRIX_OS"` in the shell script.

Locations:

- `.github/workflows/ci.yml:45`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags (e.g. @v4, @v5, @v6) instead of immutable 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the tag is moved. Unpinned references found:
- ci.yml: `actions/checkout@v4` (lines 39, 71)
- labels.yml: `actions/checkout@v4` (line 33), `crazy-max/ghaction-github-labeler@v5` (line 35)
- test.yml: `actions/checkout@v4` (line 24), `docker/bake-action@v6` (line 27), `codecov/codecov-action@v5` (line 33)
- upx-releases-json.yml: `actions/checkout@v4` (line 41), `actions/download-artifact@v4` (line 44)
- validate.yml: `actions/checkout@v4` (line 27), `docker/bake-action/subaction/list-targets@v6` (line 31), `docker/bake-action@v6` (line 46)
All of these should be pinned to full SHA digests with the tag as a comment.

Locations:

- `.github/workflows/ci.yml:39`
- `.github/workflows/ci.yml:71`
- `.github/workflows/labels.yml:33`
- `.github/workflows/labels.yml:35`
- `.github/workflows/test.yml:24`
- `.github/workflows/test.yml:27`
- `.github/workflows/test.yml:33`
- `.github/workflows/upx-releases-json.yml:41`
- `.github/workflows/upx-releases-json.yml:44`
- `.github/workflows/validate.yml:27`
- `.github/workflows/validate.yml:31`
- `.github/workflows/validate.yml:46`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script-injection in ci.yml by moving `${{ matrix.os }}` into an env var `MATRIX_OS` and referencing it as `$MATRIX_OS` in the shell script. Pinned all 12 unpinned action references across ci.yml, labels.yml, test.yml, upx-releases-json.yml, and validate.yml to full 40-character commit SHAs with original tags preserved as comments: actions/checkout@v4→11d5960a, crazy-max/ghaction-github-labeler@v5→24d110aa, docker/bake-action@v6→5be5f02f, codecov/codecov-action@v5→0fb71748, actions/download-artifact@v4→d3f86a10.

