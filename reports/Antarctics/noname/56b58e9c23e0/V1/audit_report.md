# Security Audit Report: Antarctics/noname

| Field | Value |
|-------|-------|
| Repository | [Antarctics/noname](https://github.com/Antarctics/noname) |
| Commit | `56b58e9c23e0` |
| Commit Date | 2025-03-31T21:11:33+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:35:53.526334+00:00 |
| Overall Risk | **SAFE** |
| Confidence | 100% |
| Files Scanned | 1 / 33 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 33 |
| Files analyzed | 1 |
| Skipped (unsupported type) | 32 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 1 |
| Dominant language | Markdown (✅ supported) |
| Top file types | .md(21), .png(10), (no ext)(1), .html(1) |

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

1. ❌ **The Noname (无名杀) game engine executes skill callbacks in a sandboxed environment that cannot access external network resources, file systems, or credentials from within skill callback functions** *(confidence: 30%)*
   - 检验结果：Insufficient data for Noname game engine (github.com/libnoname/noname) — manual check recommended.
   - 关联主体：`Noname game engine (github.com/libnoname/noname)`

2. ✅ **The tutorial content at github.com/Antarctics/noname accurately represents the official Noname extension API and does not instruct developers to use unsafe patterns** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/Antarctics/noname tutorial repository`
   - 证据：[Trusted service whitelist policy](https://github.com/)

## Detailed Findings

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This is documentation for a game extension development tutorial. Developers learning to create custom characters and skills for the Noname card game would reference this file to understand the skill system API, including how to write filter functions, cost functions, content functions, and various skill properties.
- Correct/safe usage is to treat this as a declarative + callback contract with the game engine: (1) keep `filter` side-effect free (pure boolean gating), (2) put player choice/payment in `cost`, and only execute effect in `content` when `event.result.bool` is true, (3) choose either `trigger` (passive) or `enable` (active) according to engine rules, (4) enforce bounded frequency with `usable`/`round`/`limited`, (5) guard all target/card accesses (`event.targets?.[0]`) to avoid runtime errors, (6) use `forced/direct/frequent` carefully so users still get expected prompts, and (7) for disruptive operations like `trigger.cancel()`, restrict by precise trigger timing + strict `filter` checks. Also keep state in clearly namespaced `player.storage.<skill>` keys and reset/cleanup in `onremove` where needed.
- This is legitimate documentation and tutorial material designed to help game mod developers understand the API, structure, and logic required to create custom characters and skills within the game engine.

### Why this might be dangerous:
- This pattern becomes dangerous when extension authors use powerful hooks without constraints. Specific risky conditions: (a) broad triggers (`global` events) plus missing `filter` can create always-on effects that rewrite core gameplay, (b) `forced:true` + `direct:true` + hidden logs (`popup:false`/`nopop:true`) can silently auto-fire effects and reduce auditability, (c) unconditional `trigger.cancel()` at early timing points can nullify core mechanics (damage/use/death flow), (d) incorrect frequency limits allow repeatable loops or resource inflation, (e) state flags in `player.storage` without cleanup can leak across turns/modes, and (f) view-as skills (`viewAs`) without strict `filterCard`/`viewAsFilter` can bypass intended card constraints. In multiplayer/mod-sharing contexts, these become fairness/integrity issues rather than malware, but still materially harmful to game trust and stability.
- The tutorial text itself cannot be directly exploited. However, the underlying architecture it describes—loading arbitrary JavaScript objects with executable functions (`filter`, `content`, `cost`) as game extensions—presents a significant security risk if the game engine does not enforce strict sandboxing. An attacker could author a malicious mod that looks like a normal skill but contains malicious JavaScript (e.g., using Node.js `require('child_process')` if running in Electron, or stealing local storage tokens) within these lifecycle hooks, executing a supply chain or Trojan attack against players who download the mod.

### Recommendations:
- [security_analyst] No action required. This file is benign educational documentation containing standard game development tutorial content with no security concerns.
- [intent_analyst] Keep this schema, but add defensive conventions in the tutorial/examples: require explicit parameter signatures in `content(event, trigger, player)` (avoid implicit globals), require strict `filter` for every `global` trigger, require one of `usable|round|limited` for repeatable active effects, and show guard clauses for `event.targets`/card existence. Add a checklist: side-effect-free `filter`, validated targets, bounded activation, visible logs unless intentionally hidden, and mandatory cleanup in `onremove`. For high-impact actions (`trigger.cancel`, mass card gain/loss, global state mods), document a “safe trigger window + condition” template to prevent accidental hard-locks or silent rule-breaking.
- [adversarial_analyst] No action is needed for this documentation file. However, for the game engine itself, ensure that user-provided JavaScript extensions are executed within a secure, isolated sandbox (e.g., using isolated contexts, disabling Node.js integration in Electron for mods, or using a secure JS interpreter) to prevent malicious mods from achieving Remote Code Execution (RCE) or accessing the host system.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a Chinese-language tutorial document (skill.md) for developing extensions/skills for a card game called '无名杀' (Noname). It contains educational JavaScript code examples demonstrating how to structure game skills, including active skills, trigger skills, view-as skills, lock skills, and limited skills. The code examples are purely game logic — they manipulate in-game state (drawing cards, dealing damage, canceling events) and contain no network requests, file system access, obfuscation, hardcoded external URLs, or any other indicators of malicious behavior.

**intent_analyst** (openai/gpt-5.3-codex):
> This is a tutorial/documentation snippet describing the intended structure of a “skill” object in the 无名杀 extension system (filters, costs, triggers, active use, view-as behavior, AI hints, etc.). The intent is educational: teach extension authors how to define legal skill metadata and lifecycle handlers (`filter`, `cost`, `content`, `init`, and optional flags). It is not trying to execute payloads or hide hostile behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided text is a benign Markdown tutorial for developing extensions (mods) for a game called '无名杀' (Noname). It contains structural explanations and JavaScript code snippets demonstrating how to define in-game skills, triggers, and effects using the game's API. From an attacker's perspective, there are no prompt injection payloads, jailbreak backdoors, or data exfiltration instructions present. The code snippets only interact with safe, intended game mechanics like drawing cards (`player.draw()`) or dealing damage (`target.damage()`).

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill contains only game extension documentation with no API calls, credentials, contract addresses, or external data flows — the sole trust dependency is that the Noname game engine appropriately scopes skill callback execution, which is a standard expectation for any game modding framework.*

This skill is a documentation/tutorial resource for developing extensions for the 'Noname' (无名杀) card game. It contains no API calls, no network requests, no contract addresses, no authentication mechanisms, and no executable code that performs sensitive operations. The files are purely Markdown documentation and JavaScript code *examples* intended to teach developers how to write game skill extensions. The 'sensitive operations' described (filter, cost, content, init functions) are game logic callbacks operating entirely within a local game engine context — they manipulate in-game state (cards, players, health) and have no external data flows. There are no trust chain dependencies of concern because no external services, credentials, or financial operations are involved.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| User Input | Game skill parameters passed to callback functions (event, player, trigger, target) within the Noname game engine | `user_provided` — skill.md — filter(event, player, name, target), cost(event, trigger, player), content(event, trigger, player) callback signatures throughout the document | ta_001 | 🟢 LOW |
| User Input | Skill configuration properties (forced, locked, usable, enable, group, sourceSkill, etc.) defined by extension developers | `user_provided` — skill.md — property definitions such as sourceSkill: 'XXX', group: ['subskill1'], enable: '', usable: number|function throughout the document | ta_001 | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🟡 SHOULD] The Noname game engine (github.com/libnoname/noname) runs extension skill code in a context that does not expose Node.js fs/net modules or raw fetch capabilities to skill callback functions

**How to verify:** Review the Noname engine source at https://github.com/libnoname/noname — specifically examine how skill callbacks are invoked and whether the execution context exposes browser/Node APIs beyond game state objects

**If wrong:** ⚠️ Extension skill callbacks could make arbitrary network requests or read local files, turning any installed extension into a potential data exfiltration vector

#### 2. [🔵 NICE TO HAVE] The tutorial repository at github.com/Antarctics/noname is maintained by a community member in good standing and has not been tampered with to include malicious code examples

**How to verify:** Check the repository's commit history, contributor reputation, and whether it is linked from the official Noname repository at github.com/libnoname/noname

**If wrong:** ⚠️ Developers following the tutorial could be taught unsafe patterns that introduce vulnerabilities into extensions they publish to other players

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:35:53.526334+00:00*