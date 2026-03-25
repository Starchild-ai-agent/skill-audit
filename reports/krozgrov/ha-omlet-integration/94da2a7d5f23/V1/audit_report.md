# Security Audit Report: krozgrov/ha-omlet-integration

> ℹ️ **NOT AN AI SKILL REPOSITORY** — No files were found matching AI skill patterns.
> This scanner targets AI agent skill repos with `.py`, `.js`, `.ts`, `.yaml`, or prompt `.md` files.
> This repository does not appear to contain auditable AI skill code.

| Field | Value |
|-------|-------|
| Repository | [krozgrov/ha-omlet-integration](https://github.com/krozgrov/ha-omlet-integration) |
| Commit | `94da2a7d5f23` |
| Commit Date | 2026-01-28T19:13:06-05:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:35:25.282569+00:00 |
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

1. ❌ **The skill's source code, when obtained, will reveal a clearly defined, benign purpose with no hidden sensitive operations** *(confidence: 30%)*
   - 检验结果：Insufficient data for Unknown skill (no files provided) — manual check recommended.
   - 关联主体：`Unknown skill (no files provided)`

2. ❌ **All API endpoints used by this skill (once source is obtained) are official, verified endpoints of reputable services** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for unknown endpoints. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`Unknown endpoints`

3. ❌ **If this skill performs blockchain operations, all contract addresses are hardcoded to verified, audited contracts and cannot be overridden by user input** *(confidence: 0%)*
   - 检验结果：Found 0 reference(s) to Unknown co... Not confirmed — verify on Etherscan or protocol docs.
   - 关联主体：`Unknown contract addresses`

## Detailed Findings

## LLM Cross-Validation Results

## Trust Chain Analysis

**Overall Trust Verdict**: 🚫 **DO_NOT_USE**
*No source files were provided for analysis, making it impossible to verify any trust assumption or assess any data flow — this skill must not be deployed or executed until its complete source code is obtained, reviewed, and re-analyzed.*

No skill files were provided for analysis — the skill directory is empty and no documentation exists. Without any source code, configuration files, or documentation, it is impossible to perform a meaningful trust chain analysis. There are no data flows to trace, no endpoints to verify, no credentials to audit, and no user inputs to assess. The complete absence of analyzable content means this skill cannot be evaluated for safety, and its purpose, behavior, and trust dependencies are entirely unknown. This represents a maximum-uncertainty situation: the skill could be trivially safe or critically dangerous, and there is no basis to distinguish between these outcomes.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | No source files found — API endpoints cannot be identified or traced | `user_provided` — No files provided for analysis. No endpoints discoverable. | ta_001 | 🔴 CRITICAL |
| Authorization | No source files found — credential handling cannot be assessed | `user_provided` — No files provided for analysis. Credential storage and transmission patterns are unknown. | ta_001 | 🔴 CRITICAL |
| User Input | No source files found — user-controlled input surfaces cannot be identified | `user_provided` — No files provided for analysis. Input validation and sanitization cannot be assessed. | ta_001 | 🔴 CRITICAL |
| Contract Addresses | No source files found — blockchain/DeFi interactions cannot be assessed | `user_provided` — No files provided for analysis. Whether this skill interacts with blockchain at all is unknown. | ta_001 | 🔴 CRITICAL |
| Calldata | No source files found — transaction construction cannot be assessed | `user_provided` — No files provided for analysis. Calldata construction patterns are unknown. | ta_001 | 🔴 CRITICAL |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The skill's source code must be obtained and reviewed before any deployment or execution

**How to verify:** 1. Locate the actual skill files in the repository or deployment package. 2. Confirm all files are present and not obfuscated. 3. Re-run this trust chain analysis with the actual source code provided.

**If wrong:** ⚠️ If the skill is deployed without source review, any sensitive operation it performs — API calls, credential handling, transaction construction, user input processing — is completely unaudited. This could result in credential theft, unauthorized transactions, data exfiltration, or arbitrary code execution depending on the skill's actual implementation.

#### 2. [🔴 MUST] The skill's documented purpose must be established and must match its actual code behavior

**How to verify:** 1. Obtain skill documentation or README. 2. Compare documented purpose against all sensitive operations found in source code. 3. Flag any operations not justified by the stated purpose.

**If wrong:** ⚠️ A skill performing operations beyond its stated purpose is a strong indicator of malicious or compromised code. Undocumented sensitive operations could include backdoors, data exfiltration, or unauthorized financial transactions.

#### 3. [🔴 MUST] No credentials, private keys, or API secrets are hardcoded in the skill's source files

**How to verify:** 1. Once source files are obtained, run a secrets scanner (e.g., truffleHog, gitleaks) against all files. 2. Manually inspect any string literals that resemble keys, tokens, or passwords. 3. Check git history for previously committed secrets.

**If wrong:** ⚠️ Hardcoded credentials would be exposed to anyone with access to the skill's source code, enabling unauthorized access to APIs, wallets, or services the skill interacts with.

#### 4. [🟡 SHOULD] The skill's dependencies (libraries, packages) are pinned to specific verified versions with no known critical CVEs

**How to verify:** 1. Once source files are obtained, locate dependency manifests (package.json, requirements.txt, go.mod, etc.). 2. Run a dependency audit (npm audit, pip-audit, etc.). 3. Verify no dependencies are fetched from unofficial or attacker-controlled sources.

**If wrong:** ⚠️ Unpinned or vulnerable dependencies could introduce supply chain attacks, allowing malicious code to execute within the skill's trust context.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:35:25.282569+00:00*