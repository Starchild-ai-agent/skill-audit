# Security Audit Report: ziwenhahaha/qqsg_struct

| Field | Value |
|-------|-------|
| Repository | [ziwenhahaha/qqsg_struct](https://github.com/ziwenhahaha/qqsg_struct) |
| Commit | `595479f4b5b9` |
| Commit Date | 2022-12-15T00:29:40+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:29:48.889950+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 72% |
| Files Scanned | 1 / 17 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 17 |
| Files analyzed | 1 |
| Skipped (unsupported type) | 16 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 1 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 1/2 code files (33%) |
| Unsupported code | C++(2) |
| Top file types | .md(12), (no ext)(3), .cpp(2) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 1 |
| 🔵 LOW | 0 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 0 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **The documented memory structures accurately reflect the actual QQ Three Kingdoms game client and have not been intentionally falsified to mislead developers into corrupting unintended memory regions** *(confidence: 30%)*
   - 检验结果：Insufficient data for qqsg_struct repository documentation — manual check recommended.
   - 关联主体：`qqsg_struct repository documentation`

2. ❌ **The author's claim that combat logic is fully server-authoritative is accurate, meaning client-side struct modifications cannot affect server-side game state (damage, defense, attack frequency)** *(confidence: 30%)*
   - 检验结果：Insufficient data for QQ Three Kingdoms game server architecture — manual check recommended.
   - 关联主体：`QQ Three Kingdoms game server architecture`

3. ❌ **The intentional omission of ComboItem lookup methods is sufficient to prevent the documented skill structures from being weaponized into functional cheats by casual actors** *(confidence: 30%)*
   - 检验结果：Insufficient data for Partial information disclosure strategy in Skill.md — manual check recommended.
   - 关联主体：`Partial information disclosure strategy in Skill.md`

## Detailed Findings

## LLM Cross-Validation Results

### 🚨 Semantic Attack Detected (No Static Anchor)

以下命中来自 **skill_prompt 语义层审查**：静态规则未给出锚点（0 findings），但 LLM 共识已识别出潜在恶意/高风险指令。

- #1 `Skill.md` | severity=MEDIUM | malicious=False

### Analysis Group #1

- **Final Severity**: MEDIUM
- **Confidence**: 72%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)
- **Semantic Attack Detected (No Static Anchor)**: 🚨 true

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file serves as reverse-engineering research documentation for a game enthusiast or developer who wants to build personal plugins, fix client-side bugs, or understand the game's internal skill system. It is part of a repository documenting game internals for educational/personal use.
- The safe/correct pattern for this kind of work is defensive and research-oriented: (1) use it in isolated environments (test accounts, private test servers, offline analysis) to understand client data models; (2) keep modifications read-only when possible (introspection, telemetry, crash/bug reproduction) instead of patching live values; (3) if mutation is necessary for bug validation, scope it to reproducible QA steps and restore state immediately; (4) treat all discovered offsets/fields as unstable and version-bound, validating structure size, alignment, and pointer safety before access; (5) document findings for responsible disclosure to the game operator, especially where client-side trust gaps are discovered. In short, this pattern is legitimate when used for compatibility tooling, diagnostics, and vulnerability reporting—not for competitive advantage.
- Game modding, creating custom UI plugins, fixing client-side bugs (as claimed by the author), or educational reverse engineering.

### Why this might be dangerous:
- The struct definitions and field offsets (e.g., yinzhiTime, LianJiCount) could be used by a malicious actor to write a game cheat/hack that modifies client-side memory to bypass stagger effects or amplify combo damage. However, the author notes these are client-side only and the game uses server-side authoritative combat, significantly limiting the impact of such abuse.
- This becomes dangerous when combined with process injection/hooking and runtime write access. Specific risky conditions include: publicly shared offsets/signatures that allow others to locate ComboItem/SkillMeta quickly; code that writes fields like yinzhiTime/LianJiCount or status/mix IDs during combat; weak client integrity checks; and any server logic that implicitly trusts client-side timing/state transitions. Under those conditions, attackers can build cheats such as control-removal automation, animation/crowd-control desync exploitation, unfair movement/skill-flow behavior, or bot scripting tied to status flags. Even if core damage is server-authoritative, client-side tampering can still enable unfair responsiveness, evasion, and automation advantages, and can be escalated if additional trust flaws exist.
- A cheat developer uses a memory scanner (like Cheat Engine) to find the base address of `ComboItem`. They then use the provided `SkillMeta` struct to write a C++ DLL or external tool that injects into the game process. The tool overwrites the `yinzhiTime` field to 0, allowing the player to bypass animation locks/hitstun, or modifies `LianJiCount` to artificially inflate damage output, ruining the game's competitive balance.

### Recommendations:
- [security_analyst] No immediate action required from a code security standpoint. This is documentation, not executable code, and poses no direct security risk to systems running it. If this repository is being evaluated for policy compliance (e.g., platform terms of service regarding game reverse engineering), that is a separate concern outside the scope of code security auditing. The self-censorship of certain details by the author is a positive indicator of good faith.
- [intent_analyst] Keep this work in a strict gray-box security research framework: publish high-level structure insights but redact exploit-enabling locator details; avoid releasing write-capable examples for live gameplay; add explicit responsible-disclosure policy and anti-abuse terms; and, technically, enforce safeguards such as read-only tooling defaults, pointer/size/runtime version checks, and audit logs for any memory mutation during testing. For the game side, prioritize server-side validation of skill timing/state, detect impossible client transitions, and add integrity/anti-tamper controls so these structures cannot be safely abused in production.
- [adversarial_analyst] Game developers should implement strict server-side validation for hitstun durations, cooldowns, and combo damage calculations rather than trusting the client. Additionally, client-side memory should be protected using anti-cheat solutions (e.g., memory integrity checks, struct obfuscation, and packing) to detect or prevent unauthorized modifications to critical game data like `SkillMeta`.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a Markdown documentation file describing internal memory structures (structs) and data offsets for the game 'QQ三国' (QQ Sanguo), a Chinese MMORPG. It documents skill-related C++ structs such as ComboItem, SkillMeta, and SkillLvlMeta, along with flag enumerations for skill types. The file is classified as a 'skill_prompt' but is actually a reverse-engineering research document. The author explicitly acknowledges the dual-use nature of this information and deliberately withholds certain details (e.g., how to find ComboItem) to reduce cheating potential. There is no executable code, no obfuscation, no data exfiltration, no hardcoded URLs, and no prompt injection. The content is gray-area in that it documents game memory structures that could theoretically be used to build cheats, but the author's stated intent is personal plugin development and bug documentation, and they actively self-censor sensitive details.

**intent_analyst** (openai/gpt-5.3-codex):
> This document is reverse-engineering metadata for QQ三国 skill-related in-memory structures (vtable layout, skill flags, status/mix IDs, timing fields) and notes which fields appear to affect client behavior. The primary intent appears to be technical understanding/modding/bug research, but the same information materially lowers the barrier to building memory-editing cheats or injected automation, especially because it identifies sensitive runtime fields and effects (e.g., stun timing, combo-related values).

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided file is reverse-engineering documentation for the game 'QQ Sanguo'. It details memory structures related to in-game skills and explicitly points out specific memory offsets that can be modified to gain unfair advantages, such as removing hitstun (`yinzhiTime`) or amplifying combo damage (`LianJiCount`). While the author claims to withhold the base pointer to prevent cheating, the provided structs and offset information significantly lower the barrier to entry for cheat developers to create memory-editing hacks (trainers) for the game. Note: Although classified as 'skill_prompt', this is a Markdown file containing C++ structs and documentation, not an AI prompt. Therefore, it contains no prompt injection, jailbreaks, or data exfiltration instructions.

#### Disagreements Between Models

- **security_analyst**: LOW - This file is a Markdown documentation file describing internal memory structures (structs) and data offsets for the game 'QQ三国' (QQ Sanguo), a Chinese MMORPG. It documents skill-related C++ structs su
- **intent_analyst**: HIGH - This document is reverse-engineering metadata for QQ三国 skill-related in-memory structures (vtable layout, skill flags, status/mix IDs, timing fields) and notes which fields appear to affect client beh
- **adversarial_analyst**: LOW - The provided file is reverse-engineering documentation for the game 'QQ Sanguo'. It details memory structures related to in-game skills and explicitly points out specific memory offsets that can be mo

## Trust Chain Analysis

**Overall Trust Verdict**: ⚠️ **VERIFY_BEFORE_USE**
*This skill contains no executable sensitive operations, API calls, or credentials — it is static reverse-engineering documentation — but its safety depends on verifying the author's key claim that QQ Three Kingdoms combat is fully server-authoritative, since the documentation explicitly identifies client-modifiable memory fields that would be cheat vectors if that claim is false.*

This skill/repository is a game reverse-engineering documentation project for 'QQ Three Kingdoms' (QQ三国), a Chinese online game. It contains C++ struct definitions, memory offsets, and game internals documentation. There are NO API calls, no network operations, no credentials, no blockchain interactions, and no executable code that performs sensitive operations. The 'sensitive' content here is purely documentation of game memory structures (skill flags, entity layouts, UI control offsets) intended for client-side plugin development. The primary trust concern is not technical data flow safety but rather the intended use of this reverse-engineering knowledge — the author explicitly disclaims cheat development and frames it as personal plugin/bug-fix tooling. No automated data flows exist that could be hijacked, poisoned, or exploited by an AI agent invoking this as a skill.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| User Input | Memory structure definitions and offsets (e.g., SkillMeta field offsets, yinzhiTime, LianJiCount) that a developer could use to locate and modify game memory at runtime | `hardcoded` — Skill.md — hardcoded struct definitions with field-level comments describing client-side modifiable values (e.g., 'yinzhiTime: 修改为0可以无视僵直效果', 'LianJiCount: 通过修改此值貌似可以实现连击的伤害增幅效果') | ta_001 | 🟡 MEDIUM |
| User Input | Skill flag bitmask enumeration (0x1 through 0x4000000) describing skill behavior categories | `hardcoded` — Skill.md — hardcoded enumeration of skill flag values with behavioral descriptions | — | 🟢 LOW |
| API Endpoints | No API endpoints present in this skill | `hardcoded` — No network calls, HTTP requests, or external service integrations exist anywhere in the repository files provided | — | 🟢 LOW |
| Authorization | No credentials, API keys, or authentication tokens present | `hardcoded` — No credentials found in any provided file. Author mentions providing lua_State base address via email contact, but this is out-of-band and not part of the skill itself. | — | 🟢 LOW |
| Contract Addresses | No blockchain or smart contract interactions present | `hardcoded` — No blockchain references anywhere in the provided files | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🟡 SHOULD] QQ Three Kingdoms combat logic is fully processed server-side with no client-authoritative values for damage, defense, or skill cooldowns

**How to verify:** Review official Tencent/QQ Three Kingdoms game architecture documentation if available, or consult network traffic analysis of game sessions to confirm that combat outcome packets originate from server replay rather than client-reported values

**If wrong:** ⚠️ If the server trusts any client-reported values that correspond to the documented modifiable struct fields (yinzhiTime, LianJiCount), those fields become functional cheat vectors affecting other players

#### 2. [🟡 SHOULD] The struct field offsets documented in Skill.md (particularly yinzhiTime at its documented position within SkillMeta, and LianJiCount) match the current live game client binary

**How to verify:** Load the current QQ Three Kingdoms client in a debugger (e.g., x32dbg/Cheat Engine), locate a SkillMeta instance, and verify field offsets match the struct layout: SkillId(0), name(+4), desc(+36), values[6](+340), Flag(+364), values2[29](+368), yinzhiTime(+484)

**If wrong:** ⚠️ Incorrect offsets used in memory patching code would write to wrong memory addresses, potentially corrupting unrelated game process memory and causing crashes

#### 3. [🔵 NICE TO HAVE] The lua_State base address sharing mentioned in the README is conducted securely (e.g., not publicly posted) and only shared with individuals the author has vetted

**How to verify:** Check the author's public communications (Baidu Tieba posts, GitHub issues, email history if accessible) to confirm the lua_State address is not publicly disclosed in any forum or repository

**If wrong:** ⚠️ A publicly available lua_State base address combined with the documented struct layouts would significantly lower the barrier to writing functional game modification tools

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:29:48.889950+00:00*