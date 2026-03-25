# Security Audit Report: namra98/exocortex

| Field | Value |
|-------|-------|
| Repository | [namra98/exocortex](https://github.com/namra98/exocortex) |
| Commit | `9f3d16b12d7a` |
| Commit Date | 2026-02-07T18:33:35+05:30 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:28:22.463828+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 70% |
| Files Scanned | 1 / 26 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 26 |
| Files analyzed | 1 |
| Skipped (unsupported type) | 25 |
| Skipped (too large) | 0 |
| Analyzed by language | shell: 1 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 1/1 code files (100%) |
| Top file types | .md(20), (no ext)(4), .ps1(1), .sh(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 0 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 0 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ✅ **The GitHub account 'namra98' and the repository 'namra98/exocortex' are controlled by the legitimate author and have not been compromised** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/namra98/exocortex`
   - 证据：[Trusted service whitelist policy](https://github.com/)

2. ❌ **raw.githubusercontent.com/namra98/exocortex/main/install.sh serves the exact file committed to the main branch of the repository without modification** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for raw.githubusercontent.com. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`raw.githubusercontent.com`

3. ❌ **GitHub (github.com and raw.githubusercontent.com) is a trustworthy code hosting platform that does not inject malicious content into served files** *(confidence: 30%)*
   - 检验结果：Insufficient data for github.com / raw.githubusercontent.com — manual check recommended.
   - 关联主体：`github.com / raw.githubusercontent.com`

4. ❌ **The NOTES-SKILL.md file in the repository contains only legitimate personal knowledge management instructions and does not include prompt injection payloads targeting the AI CLI** *(confidence: 30%)*
   - 检验结果：Insufficient data for NOTES-SKILL.md (AI operating manual) — manual check recommended.
   - 关联主体：`NOTES-SKILL.md (AI operating manual)`

5. ✅ **github is a reputable, widely-used service** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github`
   - 证据：[Trusted service whitelist policy](https://github.com/)

6. ❌ **raw is a reputable, widely-used service** *(confidence: 30%)*
   - 检验结果：Insufficient data for raw — manual check recommended.
   - 关联主体：`raw`

## Detailed Findings

## LLM Cross-Validation Results

## Trust Chain Analysis

**Overall Trust Verdict**: ⚠️ **VERIFY_BEFORE_USE**
*The skill's filesystem operations are fully justified by its personal note-taking purpose and no credentials or external APIs are involved, but the curl-pipe-bash install pattern with no integrity verification means safety depends entirely on the unverified trustworthiness of the namra98/exocortex GitHub repository — users must manually read install.sh and NOTES-SKILL.md before installation rather than executing the one-liner blindly.*

Exocortex is a personal knowledge management skill that installs itself via a one-line curl-pipe-bash pattern, cloning a GitHub repository and writing files into the user's home directory (~/.copilot/skills/ and ~/exocortex). There are no API calls, blockchain transactions, wallet addresses, or credentials involved — the skill's sensitive operations are limited to filesystem writes and a git clone from a hardcoded GitHub URL. The critical trust dependency is entirely on the integrity of the GitHub repository at github.com/namra98/exocortex: if that repo is compromised, the install script and NOTES-SKILL.md (the AI operating manual injected into the Copilot CLI context) could contain malicious instructions. The curl-pipe-bash install pattern means there is no integrity verification (no checksum, no signature) before execution. The skill's purpose (local markdown note-taking) fully justifies its filesystem operations, but the supply chain trust is weak due to the unverified remote execution model.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | GitHub raw content URL used to fetch and execute the install script directly via curl-pipe-bash | `hardcoded` — README.md — hardcoded to https://raw.githubusercontent.com/namra98/exocortex/main/install.sh and https://raw.githubusercontent.com/namra98/exocortex/main/install.ps1 | ta_001, ta_002, ta_003 | 🟠 HIGH |
| API Endpoints | GitHub repository clone URL used during installation to pull the full skill codebase | `hardcoded` — install.sh:line ~18 — hardcoded to https://github.com/namra98/exocortex.git | ta_001, ta_002, ta_004 | 🟡 MEDIUM |
| User Input | CLONE_PATH — user-supplied installation directory path passed as argument to install.sh | `user_provided` — install.sh:line 6 — CLONE_PATH="${1:-$HOME/exocortex}" | — | 🟢 LOW |
| User Input | REPO_ROOT — derived from CLONE_PATH via 'cd && pwd', injected into SKILL.md via sed substitution | `user_provided` — install.sh:line ~28 — REPO_ROOT="$(cd "$CLONE_PATH" && pwd)"; then sed "s|{{REPO_ROOT}}|$REPO_ROOT|g" writes it into SKILL.md | ta_004 | 🟡 MEDIUM |
| Authorization | No API keys, credentials, or tokens are present in this skill | `hardcoded` — No credential handling found in install.sh, README.md, or any described file | — | 🟢 LOW |
| User Input | Natural language commands passed to the AI CLI (e.g., 'save idea:', 'log:', 'add task:') that the AI interprets via NOTES-SKILL.md instructions | `user_provided` — README.md — documented usage examples; processed by AI CLI using NOTES-SKILL.md as the operating manual | ta_002, ta_004 | 🟡 MEDIUM |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The install.sh script at https://raw.githubusercontent.com/namra98/exocortex/main/install.sh should match the file committed in the repository's main branch (no injected content)

**How to verify:** 1. Open https://github.com/namra98/exocortex/blob/main/install.sh in a browser. 2. Separately fetch https://raw.githubusercontent.com/namra98/exocortex/main/install.sh with curl. 3. Compare SHA256 hashes of both. 4. Read the script manually before executing — do not pipe directly to bash without review.

**If wrong:** ⚠️ Arbitrary code executes on the user's machine with full user privileges at install time — could install backdoors, exfiltrate SSH keys, modify shell configs, etc.

#### 2. [🔴 MUST] The NOTES-SKILL.md file in the repository should contain only markdown instructions for personal note-taking operations (file reads/writes) and no instructions to exfiltrate data, call external URLs, or execute system commands

**How to verify:** 1. Open https://github.com/namra98/exocortex/blob/main/NOTES-SKILL.md directly in a browser. 2. Read the entire file. 3. Check for any instructions referencing external URLs, curl/wget commands, environment variable access, or instructions to send data anywhere. 4. Check for hidden unicode characters or obfuscated text.

**If wrong:** ⚠️ The AI CLI would follow malicious instructions on every invocation — could silently exfiltrate notes, execute shell commands, or manipulate the user into performing harmful actions.

#### 3. [🔴 MUST] The skill-template.md file should only contain a reference/pointer to NOTES-SKILL.md and the {{REPO_ROOT}} placeholder — no executable content or external references

**How to verify:** 1. Open https://github.com/namra98/exocortex/blob/main/skill-template.md in a browser. 2. Verify it only contains a path reference to NOTES-SKILL.md and no URLs, curl commands, or shell instructions.

**If wrong:** ⚠️ The generated SKILL.md installed at ~/.copilot/skills/exocortex/SKILL.md could contain malicious content that persists in the AI CLI skill system.

#### 4. [🟡 SHOULD] The GitHub account 'namra98' should show signs of being an active, legitimate developer account (account age, commit history, other repos) rather than a newly created or hijacked account

**How to verify:** 1. Visit https://github.com/namra98. 2. Check account creation date, number of repositories, commit history, and whether the account has any community trust signals (followers, contributions to other projects). 3. Check if the exocortex repo has any stars, forks, or issues that indicate community use.

**If wrong:** ⚠️ A newly created or hijacked account with a plausible-looking repo is a common supply chain attack vector — low account age or activity should increase suspicion.

#### 5. [🟡 SHOULD] The install.sh script should not contain any network calls beyond the initial git clone (no curl, wget, or other outbound connections post-clone)

**How to verify:** 1. Read install.sh fully. 2. Search for: curl, wget, nc, ncat, python -c, perl -e, or any other network primitives. 3. Verify the only network operation is 'git clone https://github.com/namra98/exocortex.git'.

**If wrong:** ⚠️ Additional network calls could exfiltrate system information, download secondary payloads, or establish persistence mechanisms.

#### 6. [🔵 NICE TO HAVE] The repository should provide a checksum file (e.g., SHA256SUMS) or GPG-signed releases so users can verify install script integrity before execution

**How to verify:** Check https://github.com/namra98/exocortex/releases for signed releases or a checksums file in the repository root.

**If wrong:** ⚠️ Without integrity verification, there is no way to detect if the script was tampered with between the time it was reviewed and the time it was executed — this is a fundamental limitation of the curl-pipe-bash pattern.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:28:22.463828+00:00*