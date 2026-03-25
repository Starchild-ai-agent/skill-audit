# Security Audit Report: guiling0/sgsrule

| Field | Value |
|-------|-------|
| Repository | [guiling0/sgsrule](https://github.com/guiling0/sgsrule) |
| Commit | `b14c66c49896` |
| Commit Date | 2020-03-24T17:25:37+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:35:33.360131+00:00 |
| Overall Risk | **SAFE** |
| Confidence | 78% |
| Files Scanned | 3 / 16 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 16 |
| Files analyzed | 3 |
| Skipped (unsupported type) | 13 |
| Skipped (too large) | 0 |
| Analyzed by language | javascript: 2, skill_prompt: 1 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 3/2 code files (100%) |
| Top file types | .md(13), .js(2), .html(1) |

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

1. ❌ **The Docute framework (loaded via index.js as 'new Docute(config)') is a legitimate, unmodified static documentation framework with no malicious behavior** *(confidence: 30%)*
   - 检验结果：Insufficient data for Docute framework — manual check recommended.
   - 关联主体：`Docute framework`

2. ❌ **The GitHub repository link referenced in the README (https://github.com/guiling0/sgsrule) is the legitimate source of this documentation and has not been tampered with** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for github.com. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`https://github.com/guiling0/sgsrule`

## Detailed Findings

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 78%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file serves as reference documentation for the Sanguosha board/card game ruleset, explaining the elements and mechanics of skills (技能) within the game. It is part of a GitHub repository (sgsrule) dedicated to documenting official game rules.
- The correct/safe usage is to treat this document as a strict semantic contract for a rules engine: (1) model skill activation as a state machine: declare skill -> select targets -> validate preconditions -> pay costs -> mark activation success -> resolve effects in written order; (2) enforce hard guards before activation (actor alive at timing point, all required targets alive, cost payable such as HP loss not exceeding current HP, and for no-cost skills at least one immediate effect executable); (3) preserve temporal context so that if a skill grants a new skill with the same timing window, the new skill can still be checked and activated in that same window; (4) apply deterministic resolution order and transactional handling so partial cost/effect execution cannot leave illegal states; (5) encode examples as conformance tests to ensure behavior matches intended rulings.
- This is a legitimate rulebook for a board game, used to clarify complex game mechanics, timing, and skill resolutions for players.

### Why this might be dangerous:
- This becomes dangerous when an implementation is loose about timing and validation boundaries. Specific conditions: if the engine validates only at declaration but not before/after cost payment, a dead actor may still resolve effects (“post-mortem activation”); if target liveness is not re-checked at required checkpoints, effects can apply to invalid/dead targets; if no-cost/no-effect constraints are skipped, players can trigger skills that should be illegal, potentially farming side triggers or causing infinite/degenerate chains; if HP-cost limits are not enforced atomically, skills can be activated with impossible costs (e.g., paying more HP than available) leading to corrupted game state; if “new skill in same timing” is implemented inconsistently, players may gain unfair extra activations or lose legal ones, causing desync between clients/judges. These are integrity/fairness risks rather than malware, but they can be severe for competitive correctness.
- There is no realistic abuse scenario for this file. It is plain text documentation for a board game and contains no executable logic or AI instructions.

### Recommendations:
- [security_analyst] No action required. This file is benign game documentation and poses no security risk.
- [intent_analyst] Translate the prose rules into machine-verifiable invariants and explicit timing checkpoints. Add a centralized `canActivate(skill, actor, timing)` validator with mandatory checks (actor alive, targets alive, cost payable, executable effect for no-cost skills), and enforce atomic transactions for cost/effect resolution with rollback on failure. Build exhaustive unit tests from every example and edge case in the text (especially death during resolution, impossible costs, empty-effect no-cost skills, and newly gained same-timing skills). Finally, document ambiguous terms (e.g., exact moment of “activation start/end”) in a formal glossary to eliminate interpreter variance.
- [adversarial_analyst] No action required. The file is completely safe and benign.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a Markdown documentation file containing official rules for the Chinese card game 'Sanguosha' (三国杀). It describes game mechanics related to skill types, activation conditions, costs, and effects. The content is entirely game rule documentation in Chinese, with no code, scripts, executable logic, prompt injection attempts, jailbreak instructions, or data exfiltration mechanisms of any kind.

**intent_analyst** (openai/gpt-5.3-codex):
> This text is a game-rules specification (not executable code) that aims to formalize how skills are classified and activated in 三国杀: state skills vs triggered skills, required elements (timing/conditions/actor/targets/cost/effects), activation order, and several edge-case constraints (cost payment limits, no-effect activation bans, alive checks, and same-timing newly gained skills). The intent is legitimate and consistency-focused, but as prose it contains implementation gray areas where different engines could interpret timing/atomicity differently.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided text is a markdown document explaining the rules of the board game 'SanGuoSha' (三国杀), specifically detailing how in-game character 'skills' (技能) work. Despite the language being classified as 'skill_prompt', this is not an AI system prompt, executable code, or automation script. It is purely informational text about board game mechanics. There are no prompt injection vulnerabilities, jailbreak backdoors, or data exfiltration instructions present.

#### Disagreements Between Models

- **security_analyst**: SAFE - This file is a Markdown documentation file containing official rules for the Chinese card game 'Sanguosha' (三国杀). It describes game mechanics related to skill types, activation conditions, costs, and 
- **intent_analyst**: LOW - This text is a game-rules specification (not executable code) that aims to formalize how skills are classified and activated in 三国杀: state skills vs triggered skills, required elements (timing/conditi
- **adversarial_analyst**: SAFE - The provided text is a markdown document explaining the rules of the board game 'SanGuoSha' (三国杀), specifically detailing how in-game character 'skills' (技能) work. Despite the language being classifie

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill performs no sensitive operations — it is a fully static, read-only game documentation viewer with no API calls, no credentials, no blockchain interactions, no user data collection, and no dynamic content construction; the only trust dependency is that the Docute rendering framework is unmodified, which is a negligible concern given that all content is public game rules.*

This skill is a static documentation viewer for the board game 'Sanguosha' (三国杀) official ruleset. It consists entirely of a frontend JavaScript configuration file (config.js), a Docute framework instantiation (index.js), and Markdown documentation files. There are no API calls, no network requests to external services, no credentials, no blockchain interactions, no user authentication, and no dynamic data inputs of any kind. The skill renders pre-written game rule documentation in a browser using the Docute static site framework. The only 'data flow' is the static configuration of navigation links and sidebar links, all of which point to local documentation paths or a hardcoded GitHub homepage URL. There are no sensitive operations whatsoever, and the trust chain is trivially simple: the content is what it claims to be — static game documentation.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | Navigation and sidebar links configured in config.js | `hardcoded` — config.js — all links are hardcoded local paths (e.g., '/docs/2/', '/docs/3/Card') or a static GitHub homepage link ('/'). No external API URLs are present. | ta_001 | 🟢 LOW |
| User Input | User navigation within the Docute documentation site | `user_provided` — index.js — Docute renders the config; users can click sidebar/nav links. All link targets are hardcoded in config.js and resolve to local markdown files. | — | 🟢 LOW |
| Authorization | No credentials, API keys, or authentication tokens of any kind | `hardcoded` — No credentials found in any file (config.js, index.js, docs/2/Skill.md). The skill is entirely public, read-only documentation. | — | 🟢 LOW |
| Calldata | No transaction data or calldata of any kind | `hardcoded` — Not applicable. This skill has no blockchain interaction, no smart contract calls, and no transaction construction logic anywhere in the codebase. | — | 🟢 LOW |
| Contract Addresses | No contract or wallet addresses of any kind | `hardcoded` — Not applicable. No addresses appear anywhere in config.js, index.js, or the markdown documentation files. | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔵 NICE TO HAVE] The Docute framework version used in this project should be an official, unmodified release from the Docute project (https://github.com/egoist/docute)

**How to verify:** Check the HTML entry point (not provided in the skill files) to see how Docute is loaded — verify the CDN URL or local file matches an official Docute release. Compare the script hash against the official release on npm or GitHub.

**If wrong:** ⚠️ A tampered Docute build could inject arbitrary JavaScript into the documentation page. Since the content is public game rules, the practical impact is very low — no credentials or sensitive data are present to steal.

#### 2. [🔵 NICE TO HAVE] The GitHub repository https://github.com/guiling0/sgsrule is the authoritative source for this skill's content and has not been forked from or replaced by a malicious actor

**How to verify:** Visit https://github.com/guiling0/sgsrule directly and confirm the repository exists, is public, and the content matches the documentation files provided in this skill.

**If wrong:** ⚠️ If the repository were hijacked, future updates to the documentation could introduce misleading game rule content. No security-sensitive impact beyond content integrity.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:35:33.360131+00:00*