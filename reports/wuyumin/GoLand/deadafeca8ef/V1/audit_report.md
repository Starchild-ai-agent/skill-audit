# Security Audit Report: wuyumin/GoLand

| Field | Value |
|-------|-------|
| Repository | [wuyumin/GoLand](https://github.com/wuyumin/GoLand) |
| Commit | `deadafeca8ef` |
| Commit Date | 2022-12-13T12:42:04+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:30:50.922331+00:00 |
| Overall Risk | **SAFE** |
| Confidence | 100% |
| Files Scanned | 1 / 7 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 7 |
| Files analyzed | 1 |
| Skipped (unsupported type) | 6 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 1 |
| Dominant language | Markdown (✅ supported) |
| Top file types | .md(6), (no ext)(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 0 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 1 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **The content in this skill accurately reflects GoLand/IntelliJ IDE behavior and does not contain misleading instructions that could cause harm** *(confidence: 30%)*
   - 检验结果：Insufficient data for GoLand IDE documentation content — manual check recommended.
   - 关联主体：`GoLand IDE documentation content`

2. ❌ **jetbrains is a reputable, widely-used service** *(confidence: 30%)*
   - 检验结果：Insufficient data for jetbrains — manual check recommended.
   - 关联主体：`jetbrains`

## Detailed Findings

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a straightforward Markdown documentation file containing GoLand IDE tips and tricks written in Chinese. It covers basic IDE usage such as multi-cursor editing, window management, keyboard shortcuts, and Git annotation features. There is no executable code, no prompt injection attempts, no hidden instructions, no data exfiltration directives, and no jailbreak backdoors. The content is entirely benign documentation.

**intent_analyst** (openai/gpt-5.3-codex):
> This content is a benign GoLand/IntelliJ productivity guide in Chinese, describing normal IDE usage patterns such as multi-cursor editing, duplicating lines, split views, optimizing imports, postfix completion, Git annotate, and switching projects. Its intent is clearly educational and focused on developer efficiency rather than executing logic, modifying systems, or performing any sensitive action.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> This file is a standard Markdown document containing tips and tricks for using the GoLand IDE (and other IntelliJ-based IDEs). It consists entirely of informational text explaining keyboard shortcuts and UI features. There are no executable scripts, no prompt injection vectors, no hidden instructions, and no mechanisms for data exfiltration. From an attacker's perspective, there is nothing to exploit here.

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill is entirely static Markdown documentation with no executable code, no API calls, no credential handling, no user input processing, and no sensitive operations of any kind — it presents zero trust chain risk and can be used safely as a reference document.*

This skill is a static documentation repository containing GoLand/IntelliJ IDE tips and tricks written in Chinese Markdown files. It performs no sensitive operations whatsoever — there are no API calls, no network requests, no credential handling, no blockchain interactions, no code execution, and no user input processing. The entire skill consists of human-readable documentation describing keyboard shortcuts, UI interactions, and IDE configuration tips. There are zero trust dependencies of any meaningful kind, as the skill is purely informational static content with no data flows to external systems.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | No API endpoints are called or referenced in any executable context. The README links to jetbrains.com as a documentation reference only. | `hardcoded` — README.md — contains a static hyperlink to https://www.jetbrains.com/go for informational purposes only; no HTTP requests are made by this skill | — | 🟢 LOW |
| Contract Addresses | No blockchain or contract addresses exist anywhere in this skill. | `hardcoded` — N/A — no blockchain content exists in any file | — | 🟢 LOW |
| Calldata | No transaction data or calldata of any kind is constructed or transmitted. | `hardcoded` — N/A — no executable code exists in this skill | — | 🟢 LOW |
| Authorization | No API keys, credentials, tokens, or authentication mechanisms are present or referenced. | `hardcoded` — N/A — no credentials exist in any file in this skill | — | 🟢 LOW |
| User Input | No user-controlled inputs are accepted, processed, or acted upon by this skill. | `user_provided` — N/A — the skill is read-only documentation; users can only read the content, not provide inputs that affect behavior | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔵 NICE TO HAVE] The JetBrains GoLand download URL referenced in README.md (https://www.jetbrains.com/go) is the official JetBrains product page

**How to verify:** Visit https://www.jetbrains.com/go in a browser and confirm it is the official JetBrains GoLand product page

**If wrong:** ⚠️ Negligible — this is a static documentation link, not a programmatic endpoint. A user would simply be directed to a wrong page, with no automated data flow or security consequence.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:30:50.922331+00:00*