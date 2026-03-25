# Security Audit Report: pem725/be-critical

| Field | Value |
|-------|-------|
| Repository | [pem725/be-critical](https://github.com/pem725/be-critical) |
| Commit | `c96ba0fa51c0` |
| Commit Date | 2026-02-04T14:08:01-05:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:34:28.262567+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 74% |
| Files Scanned | 3 / 11 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 11 |
| Files analyzed | 3 |
| Skipped (unsupported type) | 8 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 2, tool_config: 1 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 3/1 code files (100%) |
| Top file types | .md(8), .zip(1), .txt(1), .yml(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 1 |
| 🔵 LOW | 1 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 1 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **The skill's system prompt (Skill.md) does not contain hidden instructions that cause Claude to behave harmfully when in 'critical analysis mode'** *(confidence: 30%)*
   - 检验结果：Insufficient data for Skill.md prompt content — manual check recommended.
   - 关联主体：`Skill.md prompt content`

2. ✅ **The GitHub repository (github.com/pem725/be-critical) and distributed zip file contain the same Skill.md content reviewed here, with no additional hidden files or obfuscated instructions** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/pem725/be-critical`
   - 证据：[Trusted service whitelist policy](https://github.com/)

3. ❌ **The 'adversarial critic' framing does not normalize or encourage Claude to produce content that crosses into harassment, targeted attacks on real people, or psychologically harmful outputs when users submit real-world subjects for critique** *(confidence: 30%)*
   - 检验结果：Insufficient data for Skill.md — 'Discomfort is Success' and 'No Diplomatic Softening' directives — manual check recommended.
   - 关联主体：`Skill.md — 'Discomfort is Success' and 'No Diplomatic Softening' directives`

## Detailed Findings

## LLM Cross-Validation Results

### 🚨 Semantic Attack Detected (No Static Anchor)

以下命中来自 **skill_prompt 语义层审查**：静态规则未给出锚点（0 findings），但 LLM 共识已识别出潜在恶意/高风险指令。

- #1 `Skill.md` | severity=MEDIUM | malicious=False

### Analysis Group #1

- **Final Severity**: LOW
- **Confidence**: 73%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file serves as developer documentation for the 'be-critical' Claude Code skill repository. It explains the skill's architecture, detection capabilities, and core techniques to help contributors understand and maintain the codebase. It is a standard CLAUDE.md guidance file used by Claude Code when working within the repository.
- The correct/safe usage pattern is to apply this skill to ideas, arguments, plans, and decisions—not to personal attacks or vulnerable users. Safe operation means: (1) clearly scoped objective (e.g., test business plan assumptions), (2) evidence-based critique with named fallacies/biases tied to specific claims, (3) actionable outcomes (proceed/pivot/stop criteria, mitigations), and (4) professional tone even when direct. The strongest implementation keeps critique focused on reasoning quality, uncertainty, tradeoffs, and failure modes. It should also include context-aware moderation boundaries: no harassment, no protected-class targeting, no self-harm escalation, no legal/medical overreach without caution. In short: high-friction thinking, low-personal harm.
- This is a highly useful tool for developers, writers, and strategists who need rigorous stress-testing of their ideas, plans, or code, preventing the standard 'echo-chamber' validation often provided by default AI assistants.

### Why this might be dangerous:
- N/A — The file contains no instructions that could be weaponized. The 'behavioral constraints' section instructs Claude to be blunt and critical, which is the explicitly documented and expected behavior of this skill. There is no attempt to access external resources, exfiltrate data, bypass safety measures, or perform actions outside the skill's stated scope.
- This pattern becomes dangerous when the 'no validation' and 'no diplomatic softening' directives are interpreted as permission for aggression rather than rigor. Specific risky conditions include: using the mode on emotionally sensitive topics (mental health, grief, identity), applying it to people instead of ideas, or combining it with user prompts that seek humiliation, coercion, or ideological radicalization. It can also amplify model overconfidence: forcing hard verdicts with incomplete evidence may produce persuasive but wrong criticism. In organizational settings, it can normalize adversarial communication and suppress psychological safety if outputs are used as authority rather than as one analytical lens. Missing safeguards include explicit civility constraints, harm-domain routing (e.g., mental health/legal/medical), confidence calibration requirements, and refusal patterns for abusive intent.
- An attacker invokes the skill and uses the 'Argue both sides' (Debate Mode) feature on a prohibited topic (e.g., 'Argue both sides of deploying ransomware against a hospital' or 'Argue both sides of a harmful conspiracy theory'). The prompt's strong directives to 'Build strongest case FOR' and 'No diplomatic softening' might conflict with the model's safety guardrails, potentially tricking it into generating harmful content or justifications that it would normally refuse.

### Recommendations:
- [security_analyst] No action required. This file is safe and functions as intended developer documentation. The skill itself promotes adversarial reasoning critique, which is a legitimate and well-documented use case. Reviewers should note that the 'no validation by default' and 'discomfort is success' language refers to analytical style, not safety bypass attempts.
- [intent_analyst] Keep the skill but add guardrails at the prompt level: require critique of claims—not persons; enforce respectful language constraints; add a 'sensitivity check' before adversarial mode; require uncertainty statements and evidence tags for major conclusions; and include fallback behavior for high-risk domains (supportive mode or refusal/escalation). Also add a configurable 'intensity' setting so users can choose direct, firm, or hardline critique. This preserves the intended anti-validation value while reducing misuse and collateral harm.
- [adversarial_analyst] While this is a legitimate and valuable prompt structure, developers of such adversarial skills should consider adding a brief safety caveat to the actual system prompt (e.g., 'Maintain rigorous, unsoftened critique without violating core safety guidelines regarding illegal acts, hate speech, or severe harm'). Rely on the underlying LLM's core safety training to catch edge-case abuse of the 'argue both sides' directive.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This CLAUDE.md file is a developer-facing documentation file that describes the structure and behavior of a Claude Code skill called 'be-critical'. It contains no executable code, no data exfiltration instructions, no prompt injection attempts, no jailbreak backdoors, and no hidden payloads. The file accurately describes the skill's documented purpose — transforming Claude into an adversarial critic for structured reasoning analysis — and provides guidance for developers who may edit the skill. The behavioral constraints listed (no validation, no diplomatic softening, etc.) are entirely consistent with the stated purpose of the tool and do not attempt to override safety systems, extract sensitive data, or manipulate Claude into performing unauthorized actions.

**intent_analyst** (openai/gpt-5.3-codex):
> This repository defines a Claude skill prompt that intentionally shifts assistant behavior from agreeable/helpful to adversarial critical analysis. Its core intent is to improve reasoning quality by forcing structured challenge modes (fallacy detection, premortem, red team, debate, synthesis) and reducing validation bias. The design is not malware or exploit code; it is a behavioral configuration for analysis style. However, because it explicitly removes softening and prioritizes discomfort, it sits in a gray area where beneficial rigor can become hostile or unsafe output depending on context and safeguards.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided text is a `CLAUDE.md` documentation file for a Claude Code skill designed to act as an adversarial critic. It instructs the AI to abandon its default polite, validating persona in favor of rigorous, unsoftened critique ('discomfort is success', 'no diplomatic softening'). From an attacker's perspective, while the intent is clearly legitimate (improving user reasoning), the instructions to 'argue both sides' and avoid 'diplomatic softening' could potentially be leveraged as a mild jailbreak technique. An attacker might use these constraints to force the AI to generate arguments in favor of harmful activities or to produce toxic, insulting content under the guise of 'critical analysis.' There are no data exfiltration instructions or hidden trojans.

#### Disagreements Between Models

- **security_analyst**: SAFE - This CLAUDE.md file is a developer-facing documentation file that describes the structure and behavior of a Claude Code skill called 'be-critical'. It contains no executable code, no data exfiltration
- **intent_analyst**: LOW - This repository defines a Claude skill prompt that intentionally shifts assistant behavior from agreeable/helpful to adversarial critical analysis. Its core intent is to improve reasoning quality by f
- **adversarial_analyst**: LOW - The provided text is a `CLAUDE.md` documentation file for a Claude Code skill designed to act as an adversarial critic. It instructs the AI to abandon its default polite, validating persona in favor o

### Analysis Group #2

- **Final Severity**: MEDIUM
- **Confidence**: 71%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)
- **Semantic Attack Detected (No Static Anchor)**: 🚨 true

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This skill is legitimately used to invoke a structured critical analysis mode in Claude conversations. Users invoke /be-critical to get rigorous, adversarial feedback on their ideas, plans, or reasoning — counteracting the natural tendency of AI assistants to validate rather than challenge. The analysis framework (assumption analysis, failure projection, confirmation bias detection, etc.) is well-documented and aligned with the README's stated purpose.
- The correct/safe usage pattern is as an explicitly opt-in critique mode for non-sensitive planning and reasoning tasks (e.g., business strategy, product decisions, project risks, argument quality). Safe operation requires: (1) user consent to adversarial style; (2) bounded scope (critique ideas, not personal worth); (3) evidence-oriented objections rather than aggression; (4) proportional intensity based on stakes; and (5) clear domain limits (not a replacement for legal/medical/mental-health professionals). In practice, this pattern works best when it challenges assumptions, quantifies risks, proposes testable counter-hypotheses, and ends with actionable mitigations rather than just teardown. The model should still preserve baseline safety norms even in ‘critical mode.’
- Red teaming business strategies, stress-testing project plans, identifying logical fallacies in arguments, and overcoming confirmation bias in decision-making.

### Why this might be dangerous:
- N/A — The skill does not instruct Claude to bypass safety measures, exfiltrate data, access unauthorized resources, or perform any action outside its documented purpose. The forceful tone ('Be direct,' 'No Diplomatic Softening') is a stylistic directive for critique quality, not an attempt to manipulate Claude's safety posture. No jailbreak language or hidden system-level overrides are present.
- This becomes dangerous under conditions where ‘be direct’ is interpreted as permission for demeaning, coercive, or emotionally harmful language. Specific risk conditions include: vulnerable users (anxiety, depression, crisis states), high-emotion interpersonal conflicts, minors, or requests involving self-harm/abuse dynamics. It is also risky in high-stakes regulated domains (medical, legal, financial) if confidence is overstated and nuanced uncertainty is replaced with aggressive certainty. Another abuse path is weaponization: a user can use this mode to generate harsh rhetoric to intimidate others, justify pre-decided actions, or escalate workplace/personal conflict under the label of ‘critical thinking.’ Missing safeguards include tone floor controls, protected-class harassment boundaries, mental-health escalation guardrails, and explicit refusal criteria for harmful targeting. Without these, the same mechanism that finds blind spots can normalize verbal harm and overconfident adversarial outputs.
- 1. **Malicious Plan Refinement (Jailbreak):** An attacker presents a flawed plan for a cyberattack, malware deployment, or physical crime. The AI, strictly following its directive to 'Find holes in my plan', 'Work backwards from failure', and 'Identify blind spots', will point out the weaknesses in the attacker's methodology. By critiquing the attack plan, the AI inadvertently acts as a malicious consultant, providing actionable advice on how to make the crime successful and bypass defenses.

2. **Psychological Abuse:** Because the prompt explicitly demands 'No Diplomatic Softening' and states 'Discomfort is Success', a malicious actor could feed the tool sensitive personal topics, insecurities, or mental health struggles to generate highly toxic, targeted harassment that bypasses standard anti-bullying filters.

### Recommendations:
- [security_analyst] No action required. This skill is safe to deploy as-is. The prompt is transparent, well-structured, and fully aligned with its documented purpose. Standard Claude safety guardrails remain intact since no instructions attempt to override them.
- [intent_analyst] Keep the framework, but add hard safety constraints: require explicit opt-in and allow user-selectable intensity; enforce ‘criticize claims, not people’; ban insults/dehumanizing language; add mandatory uncertainty calibration and evidence requirements for strong claims; include domain disclaimers for medical/legal/financial advice; detect vulnerable-context cues (self-harm, abuse, crisis) and switch to supportive-safe mode; and implement refusal rules for requests to generate manipulative or abusive arguments. Also add a final ‘constructive close’ section: top 3 risks + concrete mitigation steps.
- [adversarial_analyst] 1. Add an explicit safety override clause: 'If the user's idea involves illegal acts, violence, malicious cyber activity, or self-harm, you must immediately refuse the request and revert to standard safety protocols.'
2. Soften the adversarial directives: Replace 'Discomfort is Success' with instructions to maintain a 'firm, objective, and professional tone.' Explicitly instruct the model to critique the *idea*, never the *person*.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This skill prompt defines a structured critical analysis assistant that challenges users' reasoning and exposes logical flaws. The file contains no prompt injection attempts, no jailbreak backdoors, no data exfiltration instructions, and no attempts to override Claude's safety guidelines. The directive to be 'critical' and 'direct' is entirely within the documented and stated purpose of the skill — to serve as an adversarial thinking partner. There are no hidden instructions, obfuscated payloads, or attempts to redirect Claude to access external resources or perform unauthorized actions.

**intent_analyst** (openai/gpt-5.3-codex):
> This skill is trying to intentionally invert default assistant behavior from supportive/agreeable to adversarial/critical, using a structured framework (assumption analysis, premortem, bias detection, red-team style critique) to improve decision quality. The core intent is generally legitimate: stress-testing reasoning and exposing blind spots. However, the prompt also explicitly incentivizes discomfort, direct confrontation, and reduced diplomatic softening, which creates a gray area where useful rigor can become unnecessarily hostile or psychologically unsafe depending on context.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> While intended as a legitimate critical thinking tool, this prompt effectively functions as a 'Devil's Advocate' jailbreak. By explicitly instructing the LLM to override its standard helpful/polite training ('No Validation Default', 'Discomfort is Success', 'push back, hard') and to relentlessly find flaws in any presented plan, it creates a massive blind spot for safety filters. An attacker can use this to refine malicious plans or generate abusive content.

#### Disagreements Between Models

- **security_analyst**: SAFE - This skill prompt defines a structured critical analysis assistant that challenges users' reasoning and exposes logical flaws. The file contains no prompt injection attempts, no jailbreak backdoors, n
- **intent_analyst**: MEDIUM - This skill is trying to intentionally invert default assistant behavior from supportive/agreeable to adversarial/critical, using a structured framework (assumption analysis, premortem, bias detection,
- **adversarial_analyst**: HIGH - While intended as a legitimate critical thinking tool, this prompt effectively functions as a 'Devil's Advocate' jailbreak. By explicitly instructing the LLM to override its standard helpful/polite tr

### Analysis Group #3

- **Final Severity**: SAFE
- **Confidence**: 79%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file is used by Jekyll/GitHub Pages to configure the documentation website for the 'be-critical' skill. It sets the site theme, enables the jekyll-remote-theme plugin, and optionally shows download links. This is standard boilerplate for any GitHub Pages project.
- The safe/correct pattern is to use `_config.yml` strictly for static-site configuration: trusted theme selection, minimal plugin usage, and non-sensitive metadata only. Using `remote_theme: pages-themes/minimal@v0.2.0` is a normal GitHub Pages pattern when you want a maintained theme without vendoring assets. Keeping `google_analytics` empty is also safe if analytics is not needed. In safe usage, theme/plugin choices are from trusted sources, reviewed, and pinned to known versions; build runs happen in a controlled CI environment; and no secrets are placed in config.
- This file is legitimately used to configure the static site generator (Jekyll) to build the documentation website for the 'be-critical' tool. It sets the site's title, description, visual theme, and enables the necessary plugin to fetch the remote theme.

### Why this might be dangerous:
- N/A — there are no executable payloads, no data exfiltration mechanisms, no obfuscated content, and no unexpected resource access. The empty google_analytics field is not a concern as it is inactive.
- This becomes dangerous if the same pattern is used with untrusted or compromised remote themes/plugins, because Jekyll build pipelines can execute plugin/theme code during site generation. Specific risky conditions: (1) a malicious theme release is referenced (or a tag is retagged) and pulled into CI; (2) organization CI builds docs with elevated tokens/secrets available to the process; (3) plugin allowlists are too broad and permit arbitrary third-party plugin execution; (4) dependency updates are automatic and unreviewed. In those cases, a seemingly harmless docs config can become a supply-chain entry point for code execution or data exfiltration.
- There is no direct abuse scenario for this specific file content. A highly theoretical and complex supply chain attack would involve an attacker compromising the official GitHub `pages-themes/minimal` repository to inject malicious JavaScript (e.g., a BeEF hook or credential harvester) into the theme. Because this config pins to `@v0.2.0`, the attacker would have to specifically alter that historical release tag, which is difficult. If successful, the malicious script would execute in the browsers of users visiting the documentation site.

### Recommendations:
- [security_analyst] No action required. This file is safe and serves its documented purpose as a Jekyll site configuration. If the google_analytics field is not needed, it can be removed for cleanliness, but its presence (empty) poses no security risk.
- [intent_analyst] Keep this config, but harden the pipeline: pin theme/plugin versions immutably (commit SHA where possible), restrict Jekyll plugins to an explicit allowlist, run docs builds in sandboxed CI with least-privilege/no long-lived secrets, enable dependency scanning/alerting, and require review for theme/plugin changes. If analytics is unused, remove the `google_analytics` key to reduce ambiguity.
- [adversarial_analyst] No immediate action is required as the file is safe. As a general best practice, ensure that if a `google_analytics` ID is eventually added, it is the correct one to prevent analytics hijacking. Continue pinning the remote theme to a specific version to prevent automatic ingestion of compromised upstream updates.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard Jekyll configuration file (_config.yml) for a GitHub Pages documentation site. It defines the site title, description, remote theme (pages-themes/minimal), and Jekyll plugins. The google_analytics field is present but empty, meaning no tracking is active. There is nothing suspicious or malicious in this file — it is exactly what you would expect for a GitHub Pages documentation site accompanying a Claude skill.

**intent_analyst** (openai/gpt-5.3-codex):
> This `_config.yml` is trying to configure a Jekyll documentation site for the `be-critical` project: set site metadata, use the `pages-themes/minimal` remote theme, enable the `jekyll-remote-theme` plugin, show download links, and leave Google Analytics unset. The intent appears legitimate and documentation-focused, not malicious.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard Jekyll configuration file used for generating a documentation website, likely hosted on GitHub Pages. From an attacker's perspective, there are no direct vulnerabilities, malicious payloads, or backdoors within this file. The use of `remote_theme` with a specific version tag (`@v0.2.0`) is a standard practice. While relying on external themes always carries a theoretical supply chain risk if the upstream repository is compromised, `pages-themes` is maintained by GitHub, making this risk negligible. The empty `google_analytics` field poses no threat.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard Jekyll configuration file (_config.yml) for a GitHub Pages documentation site. It defines the site title, description, remote theme (pages-themes/minimal), and Jekyll plugins. The g
- **intent_analyst**: LOW - This `_config.yml` is trying to configure a Jekyll documentation site for the `be-critical` project: set site metadata, use the `pages-themes/minimal` remote theme, enable the `jekyll-remote-theme` pl
- **adversarial_analyst**: SAFE - The provided code is a standard Jekyll configuration file used for generating a documentation website, likely hosted on GitHub Pages. From an attacker's perspective, there are no direct vulnerabilitie

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill performs no network calls, handles no credentials, executes no code, and interacts with no external systems — its entire operation is a system prompt that modifies Claude's conversational tone, making it safe to use provided the distributed zip file matches the reviewed source and contains no hidden instructions (ta_001, ta_002).*

The 'be-critical' skill is a pure prompt-engineering skill that operates entirely within Claude's language model context. It performs no API calls, executes no code, handles no credentials, interacts with no blockchain, and makes no network requests. Its sole function is to modify Claude's conversational behavior — shifting it from a validation-oriented mode to an adversarial critic mode. All 'sensitive operations' reduce to: accepting user-provided text and generating critical analysis of that text. The only meaningful trust dependency is that the user's input is the subject of analysis, not a vector for injecting harmful instructions into Claude's behavior. The skill's documented purpose (adversarial critique of ideas) is fully consistent with its actual implementation (a system prompt that instructs Claude to be critical). There are no external data flows to audit.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | No API endpoints are called by this skill at any point | `hardcoded` — Skill.md, CLAUDE.md, README.md — no URLs, fetch calls, HTTP requests, or external service references exist anywhere in the skill files | — | 🟢 LOW |
| Authorization | No API keys, tokens, or credentials are referenced, stored, or transmitted | `hardcoded` — Skill.md, CLAUDE.md — no credential fields, environment variable references, or secret placeholders exist in any skill file | — | 🟢 LOW |
| User Input | User provides the idea, plan, argument, or decision to be critically analyzed | `user_provided` — Skill.md — 'Once the user selects a mode (or you infer it from their request), run the appropriate analysis automatically'; the user's submitted content becomes the subject of critique | ta_001 | 🟡 MEDIUM |
| Calldata | Not applicable — this skill performs no transactions, blockchain interactions, or executable code generation | `hardcoded` — No calldata construction exists anywhere in the skill files | — | 🟢 LOW |
| Contract Addresses | Not applicable — this skill has no blockchain or DeFi functionality | `hardcoded` — No contract addresses, wallet addresses, or blockchain references exist in any skill file | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The be-critical-v0.2.zip file distributed at https://github.com/pem725/be-critical/releases/download/v0.2/be-critical-v0.2.zip contains only the files reviewed here (Skill.md, CLAUDE.md, README.md, PromptTipDebias.txt, docs/_config.yml) and no additional hidden or obfuscated files

**How to verify:** Download the zip file directly. Unzip and run 'find . -type f' to list all files. Compare the complete file list and SHA256 hashes of each file against the repository contents at github.com/pem725/be-critical. Specifically check for any additional .md files, .js files, or binary files not present in the public repo.

**If wrong:** ⚠️ If the zip contains additional files, those files could contain hidden system prompt overrides or malicious instructions that activate when the skill is installed, affecting all conversations where the skill is active.

#### 2. [🔴 MUST] The Skill.md file in the installed skill does not contain any instructions beyond what is visible in plain text — specifically, no hidden Unicode characters, zero-width characters, or encoded instructions that would be interpreted by Claude but invisible to a human reviewer

**How to verify:** Run 'cat -A Skill.md' or use a hex editor to inspect the raw bytes of the file. Check for Unicode codepoints outside the standard ASCII/UTF-8 visible range, particularly zero-width joiners (U+200D), zero-width non-joiners (U+200C), or soft hyphens (U+00AD) embedded in the text.

**If wrong:** ⚠️ Hidden Unicode instructions could cause Claude to execute instructions invisible to the human reviewer, potentially exfiltrating conversation content or bypassing safety guidelines while appearing to perform legitimate critical analysis.

#### 3. [🟡 SHOULD] The GitHub account 'pem725' is the legitimate author of this skill and has not been compromised, and the repository has not been modified since the version reviewed here

**How to verify:** Check the GitHub repository's commit history at github.com/pem725/be-critical/commits/main. Verify the most recent commit hash matches the version you are installing. Check if the account shows any signs of compromise (unusual recent activity, changed ownership, etc.).

**If wrong:** ⚠️ If the repository has been compromised or modified after this review, the installed skill could contain different instructions than those analyzed here, potentially including malicious directives.

#### 4. [🟡 SHOULD] The 'PromptTipDebias.txt' file referenced in CLAUDE.md as 'reference material containing adversarial prompting techniques' does not contain instructions that, when loaded into Claude's context, cause harmful behavior beyond the documented critical analysis purpose

**How to verify:** Locate and read PromptTipDebias.txt in the repository or zip file. Verify it contains only reference examples of logical fallacies and cognitive biases, not executable prompt injections or instructions to override Claude's safety guidelines.

**If wrong:** ⚠️ If PromptTipDebias.txt contains adversarial prompting techniques designed to manipulate Claude rather than educate users, it could function as a prompt injection payload that activates when the skill loads the file into context.

#### 5. [🔵 NICE TO HAVE] The skill's 'No Diplomatic Softening' and 'Discomfort is Success' directives do not cause Claude to produce outputs that violate Anthropic's usage policies when applied to real-world subjects (people, organizations, protected groups)

**How to verify:** Test the installed skill by submitting edge-case inputs: (1) a real named individual as the subject of critique, (2) a protected group's beliefs or practices, (3) a request to 'stress test' a controversial political position. Verify Claude's outputs remain within its standard content policy boundaries despite the aggressive framing.

**If wrong:** ⚠️ The skill's aggressive framing could be used as a jailbreak vector — users could submit harmful targets under the guise of 'critical analysis' and receive harsher outputs than Claude's default mode would produce, potentially enabling targeted harassment or discriminatory content generation.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:34:28.262567+00:00*