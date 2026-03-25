# Security Audit Report: OrbitalEnterprises/evekit-sde-client

| Field | Value |
|-------|-------|
| Repository | [OrbitalEnterprises/evekit-sde-client](https://github.com/OrbitalEnterprises/evekit-sde-client) |
| Commit | `d3fad11b1cd9` |
| Commit Date | 2022-05-20T18:53:01-04:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:24:44.992561+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 70% |
| Files Scanned | 1 / 106 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 106 |
| Files analyzed | 1 |
| Skipped (unsupported type) | 105 |
| Skipped (too large) | 0 |
| Analyzed by language | shell: 1 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 1/1 code files (100%) |
| Top file types | .md(102), (no ext)(2), .xml(1), .sh(1) |

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

1. ❌ **The 'latest online Swagger specification for the EVE Swagger Interface' referenced in the POM points to the official EVE/EveKit Swagger spec URL and has not been tampered with** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for eve swagger interface swagger spec url (in pom.xml inputspec property). Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`EVE Swagger Interface Swagger spec URL (in pom.xml inputSpec property)`

2. ❌ **The EveKit SDE service (enterprises.orbital.evekit) is a legitimate, community-trusted EVE Online static data provider with no known malicious behavior** *(confidence: 30%)*
   - 检验结果：Insufficient data for enterprises.orbital.evekit / EveKit SDE — manual check recommended.
   - 关联主体：`enterprises.orbital.evekit / EveKit SDE`

3. ❌ **The Maven Central artifact 'enterprises.orbital.evekit:sde.client' is the legitimate published artifact from the EveKit project and has not been hijacked or tampered with** *(confidence: 30%)*
   - 检验结果：Insufficient data for Maven Central: enterprises.orbital.evekit:sde.client — manual check recommended.
   - 关联主体：`Maven Central: enterprises.orbital.evekit:sde.client`

4. ❌ **The EveKit SDE API is a public, read-only, unauthenticated API for EVE Online static game data and does not require or transmit any user credentials** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for evekit sde api. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`EveKit SDE API`

## Detailed Findings

## LLM Cross-Validation Results

## Trust Chain Analysis

**Overall Trust Verdict**: ⚠️ **VERIFY_BEFORE_USE**
*The provided files themselves are low-risk (a documentation assembly shell script only), but the skill's core trust dependency — that the Swagger spec source URL in the unprovided pom.xml points to a legitimate EVE/EveKit-controlled endpoint and that the Maven Central artifact has not been hijacked — must be verified before this library is used in any production or automated agent context.*

This skill is an auto-generated Java client library for the EveKit Static Data Export (SDE) API, used to query EVE Online game data. The only code present is a post-build shell script (assemble.sh) that reorganizes documentation files and rebuilds a README. There are no API calls, credentials, wallet addresses, user inputs, or sensitive data flows present in the provided files. The actual API client code is generated at build time from a Swagger specification fetched from an online source, which is the primary trust dependency. The shell script itself performs only local file operations (rm, cp, cat) on build artifacts and poses negligible risk. The overall trust surface is extremely small for the files provided, but the build-time code generation from a remote Swagger spec introduces an indirect supply chain trust dependency.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | The Swagger/OpenAPI specification URL used to auto-generate the Java client code at build time | `config_file` — pom.xml (not provided, but referenced in README): 'inputSpec' property of the generate goal — described as 'latest online Swagger specification for the EVE Swagger Interface' | ta_001, ta_002 | 🟡 MEDIUM |
| User Input | Shell script arguments and environment — assemble.sh takes no arguments and operates only on local build artifact paths | `hardcoded` — scripts/assemble.sh — all paths are hardcoded relative paths (docs, target/generated-sources/swagger/docs, README.md, preamble.md) | — | 🟢 LOW |
| Authorization | No API keys, credentials, or secrets are present in any of the provided files | `hardcoded` — scripts/assemble.sh — no credentials present; README.md — no credentials present | ta_003 | 🟢 LOW |
| Calldata | No transaction data, calldata, or blockchain interactions are present — this is a game data API client, not a DeFi/blockchain skill | `hardcoded` — N/A — not applicable to this skill | — | 🟢 LOW |
| API Endpoints | Runtime API endpoints embedded in the generated Java client code targeting the EveKit SDE service | `external_api_response` — Generated from Swagger spec at build time — actual endpoint URLs will be in generated source files under target/generated-sources/swagger/ which are not provided | ta_001, ta_002, ta_004 | 🟡 MEDIUM |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The pom.xml inputSpec URL for the Swagger code generator points to an official EVE Online or EveKit-controlled domain (e.g., esi.evetech.net or a known EveKit GitHub URL)

**How to verify:** Inspect the pom.xml file (not provided) and locate the <inputSpec> property under the swagger-codegen-maven-plugin generate goal. Confirm the URL domain matches official EVE/EveKit sources. Cross-reference with the EveKit GitHub repository at https://github.com/OrbitalEnterprises/evekit-sde-client

**If wrong:** ⚠️ If the inputSpec URL points to an attacker-controlled server, all generated client code could be malicious, redirecting API calls or embedding backdoors in the library distributed to consumers

#### 2. [🔴 MUST] The Maven Central artifact 'enterprises.orbital.evekit:sde.client' is published by the legitimate EveKit/Orbital Enterprises project and matches the source code in the official GitHub repository

**How to verify:** 1) Search Maven Central at https://search.maven.org/artifact/enterprises.orbital.evekit/sde.client and verify the publisher. 2) Compare artifact checksums against those listed on Maven Central. 3) Cross-reference with the GitHub repository at https://github.com/OrbitalEnterprises

**If wrong:** ⚠️ A hijacked Maven artifact could execute arbitrary malicious code in any project that depends on this library, affecting all downstream consumers

#### 3. [🟡 SHOULD] The assemble.sh script is only executed in the context of a Maven build (mvn clean package) and not as a standalone script with elevated privileges

**How to verify:** Review the pom.xml exec-maven-plugin or similar configuration to confirm assemble.sh is invoked only during the package phase with standard user privileges

**If wrong:** ⚠️ If run with elevated privileges or in an unexpected working directory, the 'rm -rf docs' and file copy operations could affect unintended directories, though this risk is low given hardcoded relative paths

#### 4. [🟡 SHOULD] The EveKit SDE API endpoints generated into the client are read-only and do not support any write, authentication, or financial operations

**How to verify:** Review the generated API documentation (target/generated-sources/swagger/docs or the docs/ directory after build) and confirm all endpoints are HTTP GET operations returning static EVE game data

**If wrong:** ⚠️ If write or authenticated endpoints exist and are called by an agent, there could be unintended side effects or credential exposure beyond what is expected for a read-only game data client

#### 5. [🔵 NICE TO HAVE] The preamble.md file concatenated into README.md does not contain any malicious content or misleading instructions that could cause developers to misconfigure the library

**How to verify:** Inspect preamble.md (not provided in the skill files) for any unexpected content, URLs, or instructions that deviate from standard EveKit documentation

**If wrong:** ⚠️ Misleading documentation could cause developers to misconfigure API endpoints or security settings, though impact is low for a read-only game data library

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:24:44.992561+00:00*