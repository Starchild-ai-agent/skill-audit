# Security Audit Report: DYAI2025/omni-skills

> ℹ️ **NOT AN AI SKILL REPOSITORY** — No files were found matching AI skill patterns.
> This scanner targets AI agent skill repos with `.py`, `.js`, `.ts`, `.yaml`, or prompt `.md` files.
> This repository does not appear to contain auditable AI skill code.

| Field | Value |
|-------|-------|
| Repository | [DYAI2025/omni-skills](https://github.com/DYAI2025/omni-skills) |
| Commit | `7d047940ee20` |
| Commit Date | 2025-12-03T15:48:48+01:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:30:08.935001+00:00 |
| Overall Risk | **EMPTY_REPO** |
| Confidence | 0% |
| Files Scanned | 0 / 0 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 0 |
| Files analyzed | 0 |
| Skipped (unsupported type) | 0 |
| Skipped (too large) | 0 |

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

1. ❌ **The skill's source code, once provided, contains no malicious logic, data exfiltration, or unsafe operations** *(confidence: 30%)*
   - 检验结果：Insufficient data for Unknown skill (no files provided) — manual check recommended.
   - 关联主体：`Unknown skill (no files provided)`

2. ❌ **If this skill calls any external APIs, those endpoints are official, legitimate services and not attacker-controlled infrastructure** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for unknown api endpoints. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`Unknown API endpoints`

3. ❌ **If this skill interacts with smart contracts, all contract addresses are legitimate, audited contracts and not attacker-controlled proxies** *(confidence: 0%)*
   - 检验结果：Found 0 reference(s) to Unknown co... Not confirmed — verify on Etherscan or protocol docs.
   - 关联主体：`Unknown contract addresses`

## Detailed Findings

## LLM Cross-Validation Results

## Trust Chain Analysis

**Overall Trust Verdict**: 🚫 **DO_NOT_USE**
*No source files were provided for this skill, making it impossible to verify any trust assumption — the skill's purpose, data flows, endpoints, credential handling, and sensitive operations are entirely unknown, and deploying it would be an unauditable blind trust decision.*

No skill files were provided for analysis — the skill directory is empty and no documentation exists. Without any source code, configuration files, or documentation, it is impossible to perform a meaningful trust chain analysis. There are no data flows to trace, no endpoints to verify, no credentials to audit, and no user inputs to assess. The complete absence of analyzable content means this skill cannot be evaluated for safety, and its purpose, behavior, and risk profile are entirely unknown. This represents the highest possible uncertainty: the skill could be trivially safe or critically dangerous with no way to distinguish between the two.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| User Input | No files present — all data flows are unknown and unanalyzable | `user_provided` — No files found in skill directory — no source:line reference possible | ta_001 | 🔴 CRITICAL |
| API Endpoints | Unknown — no source files to inspect for outbound HTTP calls or API integrations | `user_provided` — No files found — cannot determine if endpoints are hardcoded, dynamic, or absent | ta_001, ta_002 | 🔴 CRITICAL |
| Authorization | Unknown — no source files to inspect for credential handling | `env_variable` — No files found — cannot determine if API keys or secrets are used, stored, or leaked | ta_001 | 🔴 CRITICAL |
| Contract Addresses | Unknown — no source files to inspect for blockchain interactions | `user_provided` — No files found — cannot determine if this skill interacts with any smart contracts | ta_001 | 🔴 CRITICAL |
| Calldata | Unknown — no source files to inspect for transaction construction | `external_api_response` — No files found — cannot determine if calldata is constructed, received, or forwarded | ta_001 | 🔴 CRITICAL |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The skill directory must contain at least one source file before any trust evaluation can be performed

**How to verify:** Re-submit the skill with all source files included (e.g., .py, .js, .ts, .json, README, requirements.txt). Confirm the file listing is non-empty before proceeding with analysis.

**If wrong:** ⚠️ If no files are ever provided, the skill cannot be trusted or deployed — its behavior is completely opaque. Any deployment would be a blind trust decision with no security basis.

#### 2. [🔴 MUST] The skill's documented purpose must be established before sensitive operations can be assessed as 'justified' or 'unjustified'

**How to verify:** Locate or request a README, docstring, or skill manifest that describes what the skill is supposed to do. Cross-reference stated purpose against actual code behavior once files are available.

**If wrong:** ⚠️ Without a stated purpose, even benign-looking operations cannot be confirmed as intentional. A skill claiming to 'check weather' that also calls a crypto API is a red flag that requires purpose documentation to evaluate.

#### 3. [🔴 MUST] Once source files are provided, all outbound network calls must be to verifiably legitimate endpoints

**How to verify:** Extract all URLs and hostnames from source code. For each: (1) search official documentation of the claimed service to confirm the domain, (2) check for HTTPS enforcement, (3) verify no IP addresses or non-standard ports are used.

**If wrong:** ⚠️ Calls to illegitimate endpoints could exfiltrate user credentials, API keys, wallet addresses, or transaction data to an attacker.

#### 4. [🟡 SHOULD] Once source files are provided, all dependencies (pip packages, npm modules, etc.) must be from official registries with no known malicious versions

**How to verify:** Extract dependency list from requirements.txt, package.json, or equivalent. Check each package on the official registry (pypi.org, npmjs.com) and cross-reference with known malicious package databases (e.g., OSV, Snyk, Socket.dev).

**If wrong:** ⚠️ Malicious dependencies could introduce supply-chain attacks — stealing credentials, exfiltrating data, or executing arbitrary code at import time.

#### 5. [🔵 NICE TO HAVE] The skill should have a version-controlled history showing its evolution and any security-relevant changes

**How to verify:** Check if the skill is hosted in a public or auditable repository (GitHub, GitLab). Review commit history for sudden additions of network calls, credential handling, or obfuscated code.

**If wrong:** ⚠️ Without version history, a previously safe skill could have been silently modified to include malicious behavior with no audit trail.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:30:08.935001+00:00*