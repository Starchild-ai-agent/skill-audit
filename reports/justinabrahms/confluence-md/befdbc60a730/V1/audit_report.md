# Security Audit Report: justinabrahms/confluence-md

> ⚠️ **CORE LANGUAGE NOT SUPPORTED** — This repo's dominant language is **Go**, which is not currently analyzed.
> Unsupported code: Go (9 files)
> Only 2 peripheral files (scripts, configs) were scanned. The core business logic was **NOT audited**.
> This result does **NOT** represent the security posture of the project. Manual review required.

| Field | Value |
|-------|-------|
| Repository | [justinabrahms/confluence-md](https://github.com/justinabrahms/confluence-md) |
| Commit | `befdbc60a730` |
| Commit Date | 2026-01-22T07:51:58-08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:24:43.230095+00:00 |
| Overall Risk | **MEDIUM (PARTIAL AUDIT — Go code not analyzed)** |
| Confidence | 14% |
| Files Scanned | 2 / 16 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 16 |
| Files analyzed | 2 |
| Skipped (unsupported type) | 14 |
| Skipped (too large) | 0 |
| Analyzed by language | tool_config: 1, shell: 1 |
| Dominant language | Go (❌ NOT SUPPORTED) |
| Code coverage | 2/11 code files (18%) |
| Unsupported code | Go(9) |
| Top file types | .go(9), .md(2), (no ext)(1), .mod(1), .sum(1), .yml(1), .sh(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 1 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 2 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **The confluence_url configured by the user points to their legitimate Atlassian/Confluence instance and has not been tampered with by a third party** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for confluence_url config value. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`confluence_url config value`

2. ❌ **The config file at ~/.config/confluence-md/config.yaml is readable only by the owning user (file permissions are restrictive, e.g. 0600)** *(confidence: 30%)*
   - 检验结果：Insufficient data for ~/.config/confluence-md/config.yaml — manual check recommended.
   - 关联主体：`~/.config/confluence-md/config.yaml`

3. ❌ **The tool validates that user-supplied page URLs share the same origin as the configured confluence_url before making authenticated requests** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for url validation logic (not visible in provided source). Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`URL validation logic (not visible in provided source)`

4. ❌ **The Confluence CQL search API properly sanitizes or parameterizes query strings, preventing CQL injection that could access unauthorized pages** *(confidence: 30%)*
   - 检验结果：Insufficient data for Atlassian Confluence CQL API — manual check recommended.
   - 关联主体：`Atlassian Confluence CQL API`

5. ❌ **In LLM-agent usage, the agent treats returned Confluence page content as data, not as instructions, preventing prompt injection from page content influencing agent behavior** *(confidence: 30%)*
   - 检验结果：Insufficient data for LLM agent consuming tool output — manual check recommended.
   - 关联主体：`LLM agent consuming tool output`

6. ❌ **The --output flag restricts write destinations to safe paths and does not allow overwriting system files or sensitive user files** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for --output flag file path handling (not visible in provided source). Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`--output flag file path handling (not visible in provided source)`

7. ❌ **The Confluence instance being queried contains only content that is appropriate for the agent/user to read and act upon** *(confidence: 30%)*
   - 检验结果：Insufficient data for Confluence instance content — manual check recommended.
   - 关联主体：`Confluence instance content`

8. ❌ **softprops/action-gh-release@v1 GitHub Action has not been compromised via tag mutation and produces release binaries that match the source code in the repository** *(confidence: 30%)*
   - 检验结果：Insufficient data for softprops/action-gh-release@v1 — manual check recommended.
   - 关联主体：`softprops/action-gh-release@v1`

## Detailed Findings

### 🔵 Finding #1: Shell: system_info

- **File**: `test\e2e_test.sh`
- **Line**: 37
- **Severity**: LOW
- **Source**: static
- **Description**: Collects system information [×5 occurrences at lines: 37, 57, 80, 90, 101]

```
--- Line 37 ---
      32 | echo "Test 1: Search for 'proposal' returns results"
      33 | OUTPUT=$($TOOL search "proposal" --limit 3 2>&1)
      34 | if echo "$OUTPUT" | grep -q "Found.*results"; then
      35 |     test_passed "Search returned results"
      36 | else
>>>   37 |     test_failed "Search did not return expected results"
      38 |     echo "$OUTPUT"
      39 | fi
      40 | echo
      41 | 
      42 | # Test 2: Search results show page titles

--- Line 57 ---
      52 | echo "Test 3: --lucky flag fetches page content"
      53 | OUTPUT=$($TOOL search "proposal" --lucky 2>&1)
      54 | if echo "$OUTPUT" | grep -q "^#"; then
      55 |     test_passed "--lucky returned markdown content"
      56 | else
>>>   57 |     test_failed "--lucky did not return markdown"
      58 |     echo "$OUTPUT"
      59 | fi
      60 | echo
      61 | 
      62 | # Test 4: --lucky with --output writes to file

--- Line 80 ---
      75 | echo "Test 5: --index fetches specific search result"
      76 | OUTPUT=$($TOOL search "proposal" --index 2 2>&1)
      77 | if echo "$OUTPUT" | grep -q "^#"; then
      78 |     test_passed "--index returned markdown content"
      79 | else
>>>   80 |     test_failed "--index did not return markdown"
      81 | fi
      82 | echo
      83 | 
      84 | # Test 6: Search with --space filter
      85 | echo "Test 6: Search with --space filter"

--- Line 90 ---
      85 | echo "Test 6: Search with --space filter"
      86 | OUTPUT=$($TOOL search "proposal" --space TECH --limit 3 2>&1)
      87 | if echo "$OUTPUT" | grep -q "Space: TECH"; then
      88 |     test_passed "--space filter worked"
      89 | else
>>>   90 |     test_failed "--space filter did not work"
      91 |     echo "$OUTPUT"
      92 | fi
      93 | echo
      94 | 
      95 | # Test 7: Search with --mine filter

--- Line 101 ---
      96 | echo "Test 7: Search with --mine filter"
      97 | OUTPUT=$($TOOL search "proposal" --mine --limit 3 2>&1 || true)
      98 | if echo "$OUTPUT" | grep -q "Found.*results\|No results found"; then
      99 |     test_passed "--mine filter worked (query executed successfully)"
     100 | else
>>>  101 |     test_failed "--mine filter did not work"
     102 |     echo "$OUTPUT"
     103 | fi
     104 | echo
     105 | 
     106 | # Test 8: Search with no results
```

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 75%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This workflow is triggered when a GitHub release is created, automatically building and publishing platform-specific binaries for the confluence-md tool so users can download pre-compiled executables from the releases page, as documented in the README.
- The safe and correct use of this pattern is a controlled, reproducible release pipeline: (1) trigger only from trusted release/tag creation paths, (2) build immutable binaries from the exact tagged commit, (3) publish only expected artifacts, and (4) use least-privilege token permissions. In this workflow, cross-compilation via `GOOS/GOARCH`, version injection through `-ldflags`, and release upload are valid practices. To use this safely in production, maintainers should ensure only trusted users can create releases/tags, verify that the checkout corresponds to the tagged commit, and explicitly enumerate expected output filenames rather than relying on broad globs. Pinning third-party actions to commit SHAs is also a best-practice for supply-chain integrity.
- This is a standard release automation script used to compile a Go application for Linux, macOS, and Windows architectures, and automatically attach the resulting binaries to a newly created GitHub Release.

### Why this might be dangerous:
- This becomes dangerous under specific conditions common to CI/CD supply-chain attacks. First, if an attacker can create or modify release tags/releases (directly, via compromised maintainer account, or via automation token abuse), they can publish trojaned binaries through this trusted pipeline. Second, use of unpinned action versions (`@v4`, `@v5`, `@v1`) creates a gray area: if an upstream action release is compromised, the workflow may execute unintended code. Third, `files: confluence-md-*` may upload unintended files if a malicious or accidental file matching that pattern exists in the workspace, causing artifact poisoning. Fourth, the workflow grants `contents: write`; if any step or action is compromised, that permission can be abused to alter releases. None of these are proof of malicious intent here, but they are realistic escalation conditions.
- An attacker who gains write access to the repository could push malicious code and create a new tag, leveraging this workflow to automatically build and distribute malware to end-users under the guise of an official release. Additionally, if the third-party `softprops/action-gh-release@v1` action is compromised by a malicious actor, the release process could be hijacked to swap the legitimately built binaries with backdoored versions during the upload phase.

### Recommendations:
- [security_analyst] No action required. The workflow is well-structured and follows security best practices: minimal permissions, pinned action versions, use of built-in GITHUB_TOKEN, and no injection of untrusted input into shell commands beyond the version tag (which is sourced from the GitHub ref). One minor improvement would be to pin the softprops/action-gh-release@v1 action to a specific commit SHA for supply chain security hardening, but this is a low-priority best practice rather than a vulnerability.
- [intent_analyst] Keep the workflow but harden it: pin all actions to immutable commit SHAs; restrict who can create tags/releases (branch protections, required reviews, environment protections); verify the release tag and commit consistency in a dedicated validation step; replace `files: confluence-md-*` with an explicit artifact list per matrix target; and reduce permissions where possible (use job-level minimal permissions and only grant `contents: write` to the upload step/job). Optionally add checksum/signature generation (e.g., SHA256 + cosign) and provenance/SLSA attestation to improve trust in released binaries.
- [adversarial_analyst] To mitigate supply chain risks, pin third-party GitHub Actions (like `softprops/action-gh-release`, `actions/checkout`, and `actions/setup-go`) to specific, verified commit SHAs rather than mutable version tags (e.g., `@v1`). Additionally, enforce strict branch protection rules and require signed commits to ensure only reviewed and authorized code can be tagged for release.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard GitHub Actions release workflow for a Go-based CLI tool called confluence-md. It builds cross-platform binaries for Linux, macOS, and Windows across multiple architectures, then uploads them as release assets using the official softprops/action-gh-release action. The workflow uses pinned, well-known actions (actions/checkout@v4, actions/setup-go@v5), scopes permissions to only 'contents: write' (necessary for uploading release assets), and uses the built-in GITHUB_TOKEN secret rather than any hardcoded credentials. There is no obfuscation, no exfiltration to external URLs, and no unexpected system access.

**intent_analyst** (openai/gpt-5.3-codex):
> This workflow is intended to perform legitimate release automation: when a GitHub Release is created, it checks out the repository, builds Go binaries for multiple OS/architecture targets, injects a version string from the tag into the binary, and uploads all produced artifacts to the release. The overall intent is standard CI/CD packaging and distribution, not malicious behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided GitHub Actions workflow is a standard, legitimate CI/CD pipeline for building and releasing Go binaries across multiple platforms. From an attacker's perspective, there are no direct vulnerabilities (such as command injection) within the YAML itself, primarily because Git strictly limits the characters allowed in tag names (which populate the `GITHUB_REF` variable used in the build step). The main attack vectors associated with this file are standard supply chain risks, such as compromising the underlying source code before a release is triggered, or hijacking the third-party actions used in the workflow.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard GitHub Actions release workflow for a Go-based CLI tool called confluence-md. It builds cross-platform binaries for Linux, macOS, and Windows across multiple architectures, then upl
- **intent_analyst**: LOW - This workflow is intended to perform legitimate release automation: when a GitHub Release is created, it checks out the repository, builds Go binaries for multiple OS/architecture targets, injects a v
- **adversarial_analyst**: SAFE - The provided GitHub Actions workflow is a standard, legitimate CI/CD pipeline for building and releasing Go binaries across multiple platforms. From an attacker's perspective, there are no direct vuln

### Analysis Group #2

- **Final Severity**: SAFE
- **Confidence**: 77%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This script is used as part of the confluence-md project's CI/testing pipeline to verify that the compiled binary behaves correctly against a real Confluence instance. It is a typical integration/e2e test harness.
- The safe/correct usage pattern here is: run a known local binary (`./confluence-md`) in a controlled test environment, assert expected behavior from deterministic fixtures/accounts, and print minimal diagnostics on failure. Capturing output via `OUTPUT=$(...)` is appropriate for assertions; using helper functions (`test_passed`, `test_failed`) is good for readable reporting. For safe operation, keep credentials out of stdout/stderr, run tests against non-production data, and prefer stable assertions (e.g., explicit machine-readable output modes if available). Keep temporary-file handling scoped and cleaned (`mktemp` + `trap`), and use strict shell options (`set -euo pipefail`) with deliberate `|| true` only where non-zero exits are expected and explicitly asserted.
- This is a standard test script used in CI/CD pipelines or local development to ensure the `confluence-md` tool functions correctly across various use cases (searching, filtering, outputting to files).

### Why this might be dangerous:
- This pattern becomes dangerous under specific conditions: (1) if the CLI prints secrets/tokens (especially with `--debug`), the script’s `2>&1` capture and `echo "$OUTPUT"` can leak credentials into CI logs; (2) if Confluence content includes terminal control sequences, blindly echoing output can enable log/terminal escape injection or misleading test logs; (3) if tests run against production with broad queries, they may unintentionally expose sensitive page titles/metadata in artifacts; (4) with `set -e`, unhandled non-zero exits in command substitutions can terminate the script before expected assertions, creating false confidence or skipped checks. These are not malicious behaviors by themselves, but they can create security and reliability issues when environment and data are not tightly controlled.
- There is no viable abuse scenario for this script as it stands. It does not accept user input, meaning command injection is not possible. An attacker would need write access to the file system to modify the script and introduce malicious commands, at which point the system is already compromised.

### Recommendations:
- [security_analyst] No action required. The static analysis findings are false positives caused by the rule incorrectly matching the `test_failed` function calls as system information collection. The script is safe and appropriate for its stated purpose.
- [intent_analyst] Keep the test script, but harden it: (a) redact or suppress sensitive output before printing failures (e.g., filter tokens/password patterns; avoid full raw dumps by default), (b) sanitize non-printable/control characters before echoing captured content, (c) use `set -euo pipefail` and explicitly wrap expected-failure commands with comments and assertions, (d) add `trap 'rm -f "$TMPFILE"' EXIT` for guaranteed cleanup, (e) prefer stable/machine-readable assertions over loose regex on human text, and (f) run in a dedicated low-privilege test workspace with non-sensitive Confluence data. The specific flagged `test_failed` lines are safe and should be treated as normal test bookkeeping, not malicious activity.
- [adversarial_analyst] No changes are necessary. The script follows standard shell testing practices and securely handles temporary files. The static analysis warning can be safely ignored.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard end-to-end test script for the confluence-md CLI tool. It exercises the tool's documented features (search, --lucky, --output, --index, --space, --mine, --debug, --include-metadata flags) by running the binary and checking its output against expected patterns. The static analysis flags for 'system_info collection' are false positives — the flagged lines are simply calls to a local `test_failed()` shell function that prints a colored failure message and increments a counter. There is no actual system information collection, no data exfiltration, no access to unrelated resources, and no obfuscation.

**intent_analyst** (openai/gpt-5.3-codex):
> This script is an end-to-end test runner for the `confluence-md` CLI: it executes common search/fetch flows, checks output patterns with `grep`, tracks pass/fail counts, and exits non-zero if any test fails. The flagged lines are not system reconnaissance; they are ordinary failure-reporting branches. The intent is legitimate QA automation, but some patterns (capturing and echoing full command output, broad stderr capture, and `set -e` behavior) sit in a gray area where they are useful for debugging yet can become risky or brittle in CI.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard end-to-end (E2E) bash test script for the `confluence-md` command-line tool. It executes the tool with hardcoded parameters and verifies the output using standard shell commands like `grep`. There are no external inputs being passed to the script that could lead to command injection, and temporary files are handled securely using `mktemp`. The static analysis finding claiming it 'collects system information' is a false positive triggered by the `test_failed` function calls.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard end-to-end test script for the confluence-md CLI tool. It exercises the tool's documented features (search, --lucky, --output, --index, --space, --mine, --debug, --include-metadata 
- **intent_analyst**: LOW - This script is an end-to-end test runner for the `confluence-md` CLI: it executes common search/fetch flows, checks output patterns with `grep`, tracks pass/fail counts, and exits non-zero if any test
- **adversarial_analyst**: SAFE - The provided code is a standard end-to-end (E2E) bash test script for the `confluence-md` command-line tool. It executes the tool with hardcoded parameters and verifies the output using standard shell

## Trust Chain Analysis

**Overall Trust Verdict**: ⚠️ **VERIFY_BEFORE_USE**
*The skill's read-only Confluence access purpose is legitimate and proportionate, but safe use requires verifying that the tool enforces same-origin URL validation before attaching credentials (ta_003/ta_006), that the --output flag cannot write to sensitive paths (ta_006), and that the release pipeline is not vulnerable to supply chain compromise via unpinned GitHub Actions (ta_008) — none of which can be confirmed from the provided source files alone.*

This skill is a Confluence page fetcher that reads credentials (API token + email) from a local config file or environment variables, constructs API calls to a user-configured Confluence instance, and returns page content as Markdown. There are no blockchain/DeFi operations, no contract addresses, and no calldata. The sensitive operations are: (1) reading API credentials from disk/env, (2) making authenticated HTTP requests to a Confluence instance whose base URL is user-configured, and (3) passing user-supplied search queries and URLs as parameters to that API. The key trust dependencies are that the configured Confluence URL is the legitimate instance the user intends to query, that credentials are stored safely and not leaked, and that user-supplied search terms or URLs cannot be weaponized to exfiltrate data to unintended endpoints. Because the Confluence base URL is fully user-controlled via config, a misconfigured or maliciously injected URL could cause the tool to send credentials to an attacker-controlled server. The skill's purpose (read-only Confluence access) is straightforward and the sensitive operations are proportionate to that purpose.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| Authorization | Confluence API token and email used for HTTP Basic Auth against the Confluence REST API | `config_file` — README documents ~/.config/confluence-md/config.yaml fields: api_token, email. Also readable from environment variables (implied by README). No source code provided to confirm exact file:line. | ta_001, ta_002 | 🟡 MEDIUM |
| API Endpoints | Base Confluence URL (confluence_url) used to construct all API requests | `config_file` — README: ~/.config/confluence-md/config.yaml field confluence_url, e.g. https://your-domain.atlassian.net/wiki. No hardcoded fallback URL visible in provided source. | ta_001, ta_003 | 🟠 HIGH |
| User Input | Search query string passed to Confluence search API (e.g. 'proposal' in test suite) | `user_provided` — test/e2e_test.sh lines: $TOOL search "proposal" — query is a CLI argument passed directly to the tool. In agent contexts, this would be LLM-generated. | ta_004, ta_005 | 🟡 MEDIUM |
| User Input | Confluence page URL passed directly to fetch a specific page | `user_provided` — README: 'Fetch by URL: Download a Confluence page directly using its URL'. URL is a CLI argument. No source file provided showing URL validation. | ta_001, ta_006 | 🟠 HIGH |
| User Input | --space, --mine, --index, --limit, --output flags controlling search scope and file output path | `user_provided` — test/e2e_test.sh: --space TECH, --mine, --index 2, --limit 3, --output "$TMPFILE" flags demonstrated. --output writes fetched content to a user-specified file path. | ta_005, ta_007 | 🟡 MEDIUM |
| Authorization | GITHUB_TOKEN used in release workflow to upload binaries | `env_variable` — .github/workflows/release.yml line: env: GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} — used by softprops/action-gh-release@v1 | ta_008 | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The tool validates that URLs passed to the fetch-by-URL command share the same origin (scheme + host) as the configured confluence_url before attaching Authorization headers

**How to verify:** Review the Go source code (not provided) for the fetch/URL command handler. Search for URL parsing logic and confirm the host of the supplied URL is compared against the configured base URL host before the HTTP request is made with credentials.

**If wrong:** ⚠️ SSRF vulnerability: an agent or user can supply any URL (e.g., http://169.254.169.254/latest/meta-data/ or an attacker server) and the tool will make an authenticated request to it, exfiltrating the API token

#### 2. [🔴 MUST] The --output flag does not allow writing to paths outside a safe directory (e.g., it should reject absolute paths to system locations or paths containing '..')

**How to verify:** Review Go source for the --output flag handler. Check whether the output path is validated against a safelist or restricted to relative paths / a designated output directory.

**If wrong:** ⚠️ Arbitrary file write: an agent supplying --output ~/.ssh/authorized_keys combined with a Confluence page containing a crafted SSH public key could establish persistent unauthorized access

#### 3. [🔴 MUST] The GitHub Actions release workflow pins softprops/action-gh-release to a specific commit SHA rather than a mutable tag (@v1)

**How to verify:** Check .github/workflows/release.yml — currently shows 'uses: softprops/action-gh-release@v1'. Compare against the current commit SHA for v1 at https://github.com/softprops/action-gh-release/tags. A pinned SHA would look like softprops/action-gh-release@de2c0eb.

**If wrong:** ⚠️ Supply chain attack: if the @v1 tag is moved to a malicious commit, all future releases will upload trojaned binaries to GitHub Releases, affecting every user who installs via the documented curl commands

#### 4. [🔴 MUST] The config file ~/.config/confluence-md/config.yaml is created with restrictive permissions (0600 or equivalent) so only the owning user can read it

**How to verify:** On a system with the tool installed, run: ls -la ~/.config/confluence-md/config.yaml and confirm permissions show -rw------- (0600). Also check if the tool itself sets these permissions when creating the file.

**If wrong:** ⚠️ Local credential theft: any process or user on the same machine can read the plaintext API token and gain full authenticated access to the Confluence instance

#### 5. [🟡 SHOULD] The Confluence search query is passed as a properly encoded parameter to the Confluence REST API, not interpolated into a raw CQL string in a way that allows injection

**How to verify:** Review Go source for the search command. Confirm the query string is passed as a URL query parameter (e.g., ?cql=text+%3D+%22...%22 with proper encoding) rather than raw string concatenation into a CQL expression.

**If wrong:** ⚠️ CQL injection: a crafted search query could access Confluence pages outside the user's intended scope or bypass space-level access controls

#### 6. [🟡 SHOULD] The go.sum file in the repository pins all Go module dependencies to specific cryptographic hashes, preventing dependency confusion or substitution attacks

**How to verify:** Check that go.sum exists in the repository root and contains SHA-256 hashes for all dependencies listed in go.mod. Verify no dependencies are sourced from unofficial module proxies.

**If wrong:** ⚠️ Dependency confusion attack: a malicious Go module with the same name could be substituted during build, producing backdoored binaries

#### 7. [🔵 NICE TO HAVE] The tool does not log or print the API token or email in debug output (--debug flag referenced in e2e tests)

**How to verify:** Run the tool with --debug flag against a test instance and inspect stderr/stdout for any output containing the api_token value or full Authorization header.

**If wrong:** ⚠️ Credential leakage: debug logs containing the API token could be captured in CI logs, terminal history, or log aggregation systems

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:24:43.230095+00:00*