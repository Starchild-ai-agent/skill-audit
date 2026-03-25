# Security Audit Report: wpz2020/xinghuodocs

| Field | Value |
|-------|-------|
| Repository | [wpz2020/xinghuodocs](https://github.com/wpz2020/xinghuodocs) |
| Commit | `3e997f715307` |
| Commit Date | 2025-06-04T10:25:11+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:34:03.838079+00:00 |
| Overall Risk | **SAFE** |
| Confidence | 78% |
| Files Scanned | 1 / 300 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 300 |
| Files analyzed | 1 |
| Skipped (unsupported type) | 420 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 1 |
| Dominant language | Markdown (✅ supported) |
| Top file types | .md(300) |

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

1. ❌ **This skill file is purely documentation and contains no hidden executable logic beyond what is visible in the markdown content** *(confidence: 30%)*
   - 检验结果：Insufficient data for Skill.md / xinghuodocs skill package — manual check recommended.
   - 关联主体：`Skill.md / xinghuodocs skill package`

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
- This file serves as API reference documentation for developers building game skills/abilities on the xinghuodocs platform. Developers would consult this to understand available methods and properties of the Skill class when writing TypeScript game logic.
- This is standard API reference documentation used to provide context to an AI assistant or human developers writing game logic, custom maps, or mods.
- The safe pattern is to treat server-side `Skill` mutation APIs as authoritative gameplay controls invoked only by trusted game logic: use `can_cast(...)` before cast flows, then apply `set_cd/active_cd` within bounded rules; use `set_level/add_level/learn` only through progression systems with max-level checks; use `set_phase` only in validated multi-stage skill state machines; use `ignore_pay_cd` and `set_cd_force` only for explicit, audited mechanics (e.g., scripted buffs, debug tooling gated to admin/test environments). On the client side, methods should be read-only UI/state presentation (`get_current_cd`, `get_tip`, `is_toggled_on`) and never treated as security decisions. Correct usage also includes null/validity checks on owners/targets/items, enum validation for preset attributes, and clamping numeric inputs (cooldown >= 0, level/stack within designed bounds).

### Why this might be dangerous:
- As this is purely reference documentation, there is no direct abuse scenario against the AI or the system parsing it. An attacker could potentially use the knowledge of this API to write a game cheat or exploit within the game engine itself (e.g., abusing `set_cd(0)` to remove cooldowns if the game server lacks proper validation), but the documentation file itself is completely benign.
- This becomes dangerous if untrusted inputs (client packets, mod scripts, user-generated events) can directly trigger mutators like `set_cd_force`, `ignore_pay_cd`, `set_level`, `event_notify`, or `set_option`. Under those conditions, attackers could bypass cooldown/cost, force instant recasts, over-level skills, spam state transitions, or desync client/server state for unfair advantage. Specific risk conditions include: missing server authority boundaries; no permission model for debug/admin-only methods; absent rate limits on event/cast-related calls; lack of parameter validation (negative cooldown, out-of-range phase, invalid target unions); and reliance on client-reported cooldown/learnability. In competitive or economy-sensitive games, this can translate into gameplay cheating, progression corruption, and potential service instability from event abuse.

### Recommendations:
- [security_analyst] No action required. This file is benign documentation and poses no security risk.
- [adversarial_analyst] No action required. The documentation is safe to be ingested by AI tools or displayed to users.
- [intent_analyst] Keep all write operations server-authoritative and gate sensitive methods (`set_cd_force`, `ignore_pay_cd`, `set_level`, `set_option`, `event_notify`) behind explicit permission checks and environment flags (dev/test only where applicable). Add strict validation/clamping for every numeric/state input, enforce finite-state transitions for multi-phase skills, and require precondition checks (`can_cast`, ownership, alive/valid target, range). Log and audit privileged skill mutations, add per-skill/per-player rate limiting for cast/event pathways, and never trust client-side `Skill` values for enforcement. If possible, split API surface into public gameplay-safe methods vs internal/admin/debug methods to reduce accidental misuse.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a technical API documentation page (Skill.md) for a game development platform called 'xinghuodocs'. It documents the Skill class API for both server-side and client-side TypeScript, listing methods and properties such as get_level(), set_cd(), add_stack(), etc. The content is purely reference documentation written in Chinese, containing no executable code, no prompt injection instructions, no jailbreak attempts, no data exfiltration directives, and no hidden payloads. It is a straightforward Markdown documentation file.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided text is purely technical documentation for a TypeScript API related to a game engine's 'Skill' system. It lists various server-side and client-side methods and properties (e.g., managing cooldowns, levels, stacks, and damage notifications). There are no executable components, prompt instructions, prompt injections, jailbreaks, or hidden malicious payloads present in this text.

**intent_analyst** (openai/gpt-5.3-codex):
> This content is API documentation (not executable code) for a game-engine `Skill` class on both server and client sides. The intent is to expose lifecycle/control methods for skills (cooldown, level, visibility, casting checks, phase/state, stack, autocast, etc.) so gameplay logic can read and mutate skill state. There is no direct malicious payload here, but several methods are inherently privileged and can be used either as valid game mechanics or as exploit vectors depending on where and how they are called.

#### Disagreements Between Models

- **security_analyst**: SAFE - This file is a technical API documentation page (Skill.md) for a game development platform called 'xinghuodocs'. It documents the Skill class API for both server-side and client-side TypeScript, listi
- **adversarial_analyst**: SAFE - The provided text is purely technical documentation for a TypeScript API related to a game engine's 'Skill' system. It lists various server-side and client-side methods and properties (e.g., managing 
- **intent_analyst**: LOW - This content is API documentation (not executable code) for a game-engine `Skill` class on both server and client sides. The intent is to expose lifecycle/control methods for skills (cooldown, level, 

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill contains only static game engine API documentation with no executable code, no network calls, no credential handling, and no runtime data processing — there are no sensitive operations to evaluate trust for, provided the package contains no hidden executable files beyond the visible markdown content.*

This skill is a documentation-only artifact — it contains no executable code, no API calls, no credential handling, no blockchain interactions, and no user input processing. The entire content consists of Chinese-language technical documentation describing the API surface of a 'Skill' class in a game server TypeScript SDK (likely a MOBA or RPG game engine). All 'sensitive operations' listed (set_cd, set_level, remove, notify_damage, etc.) are game engine method signatures documented for developer reference, not actual implementations. There is no data flow to analyze in the traditional security sense: no endpoints are called, no keys are stored, no calldata is constructed, and no user inputs are processed. The trust chain is trivially safe because the skill performs no operations at runtime — it is purely static reference documentation.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| User Input | Method parameters described in documentation (e.g., cooldown numbers, level values, stack counts) | `user_provided` — Skill.md — method signatures such as active_cd(冷却上限:number, 无视冷却缩减:boolean), add_level(等级:number), set_cd(冷却:number) | — | 🟢 LOW |
| API Endpoints | Internal documentation hyperlinks to method detail pages | `hardcoded` — Skill.md — relative URLs such as /技术文档/服务端TS API/Skill/方法/active_cd embedded as markdown links | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The skill package contains ONLY the documentation files shown (Skill.md / README content) and no additional executable scripts, configuration files, or network-calling code

**How to verify:** Enumerate all files in the skill package directory. Confirm no .ts, .js, .py, .sh, or config files (e.g., package.json with postinstall scripts) exist beyond the markdown documentation files provided.

**If wrong:** ⚠️ If hidden executable files exist, this entire analysis is incomplete. Those files could contain API calls, credential handling, or data exfiltration that was not reviewed — the trust verdict would need to be re-evaluated from scratch.

#### 2. [🟡 SHOULD] The 'xinghuodocs' skill is an official documentation mirror for the Xinghuo (星火) game engine SDK and not a spoofed or tampered documentation package

**How to verify:** Cross-reference the documented API methods (active_cd, add_level, notify_damage, etc.) against the official Xinghuo game engine developer portal or SDK release notes to confirm the documentation is accurate and unmodified.

**If wrong:** ⚠️ If the documentation is tampered, developers using it as reference could implement incorrect or insecure patterns in their own game scripts — indirect risk to downstream code quality, not a direct runtime risk from this skill itself.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:34:03.838079+00:00*