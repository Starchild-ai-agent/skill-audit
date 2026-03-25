# Security Audit Report: crazycs520/note

| Field | Value |
|-------|-------|
| Repository | [crazycs520/note](https://github.com/crazycs520/note) |
| Commit | `53f32b90e89f` |
| Commit Date | 2020-06-01T00:50:28+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:30:58.168360+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 82% |
| Files Scanned | 2 / 88 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 88 |
| Files analyzed | 2 |
| Skipped (unsupported type) | 86 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 2 |
| Dominant language | Markdown (✅ supported) |
| Top file types | .png(47), .md(36), .jpg(3), (no ext)(1), .docx(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 1 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 1 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh serves the legitimate, unmodified shadowsocks installer authored by teddysun** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for raw.githubusercontent.com. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh`

2. ✅ **The GitHub account 'teddysun' and repository 'shadowsocks_install' are controlled by the legitimate original author and have not been compromised or transferred to a malicious party** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/teddysun/shadowsocks_install`
   - 证据：[Trusted service whitelist policy](https://github.com/)

3. ❌ **The --no-check-certificate flag does not expose the download to an active MITM attack in the network path between the VPS and GitHub** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for wget --no-check-certificate flag usage. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`wget --no-check-certificate flag usage`

4. ❌ **github.com/teddysun/across/raw/master/bbr.sh serves the legitimate BBR kernel installation script authored by teddysun** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for github.com. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`https://github.com/teddysun/across/raw/master/bbr.sh`

5. ✅ **The GitHub account 'teddysun' and repository 'across' are controlled by the legitimate original author and have not been compromised** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/teddysun/across`
   - 证据：[Trusted service whitelist policy](https://github.com/)

6. ❌ **The scripts at these URLs have not been silently updated since this skill was authored (2017) to include malicious or breaking changes** *(confidence: 30%)*
   - 检验结果：Insufficient data for master branch tip of both teddysun repositories — manual check recommended.
   - 关联主体：`master branch tip of both teddysun repositories`

7. ✅ **github is a reputable, widely-used service** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github`
   - 证据：[Trusted service whitelist policy](https://github.com/)

8. ❌ **raw is a reputable, widely-used service** *(confidence: 30%)*
   - 检验结果：Insufficient data for raw — manual check recommended.
   - 关联主体：`raw`

## Detailed Findings

## LLM Cross-Validation Results

### 🚨 Semantic Attack Detected (No Static Anchor)

以下命中来自 **skill_prompt 语义层审查**：静态规则未给出锚点（0 findings），但 LLM 共识已识别出潜在恶意/高风险指令。

- #1 `skill.md` | severity=LOW | malicious=False

### Analysis Group #1

- **Final Severity**: LOW
- **Confidence**: 64%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)
- **Semantic Attack Detected (No Static Anchor)**: 🚨 true

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This skill/documentation is legitimately used by individuals who want to set up a Shadowsocks proxy server on a Vultr VPS for privacy, censorship circumvention, or secure tunneling purposes. BBR is a legitimate TCP optimization algorithm developed by Google. The referenced scripts by 'teddysun' are well-known in the open-source community for this purpose.
- The safe/correct pattern is to treat this as infrastructure automation with strict supply-chain controls. Use official repositories or pinned release artifacts instead of mutable raw URLs; do NOT use `--no-check-certificate`; verify TLS, checksums, and (ideally) signatures before execution; review script contents first; pin to a known commit/tag; run in a disposable test VM before production. Restrict service exposure (firewall allowlist, non-default ports where appropriate, strong credentials/encryption settings, fail2ban/rate limiting, minimal open ports), keep the OS and packages patched, and ensure legal/policy compliance for proxy usage in your jurisdiction and organization. For BBR, confirm kernel compatibility and rollback plan before applying kernel/network changes.
- This code is a standard (albeit insecurely written) tutorial/script for setting up a Shadowsocks proxy server and enabling TCP BBR congestion control on a Linux VPS, commonly used to bypass internet censorship and improve connection speeds.

### Why this might be dangerous:
- The primary gray-area concern is that Shadowsocks is a censorship circumvention tool, which is illegal in some countries. Additionally, the pattern of downloading and executing remote shell scripts with --no-check-certificate bypasses TLS verification, meaning if the remote server or GitHub were compromised, malicious code could be executed. However, this is a documentation file, not an automated agent that executes these commands — a human must choose to run them.
- This becomes dangerous under several conditions: (1) network-path tampering or DNS poisoning, because `--no-check-certificate` allows man-in-the-middle delivery of a trojanized script; (2) upstream compromise of the GitHub account/repo or silent script changes on `master`, causing arbitrary root-level code execution; (3) deploying Shadowsocks as an open or weakly protected proxy, enabling unauthorized relay, criminal abuse, and operator liability; (4) using this to intentionally evade enterprise/government controls in violation of policy/law; (5) running kernel-tuning scripts (BBR) on unsupported systems, potentially degrading connectivity or stability. Missing safeguards include integrity pinning, code review, least-privilege execution boundaries, explicit authentication hardening, network access controls, monitoring/auditing, and change-management/rollback controls.
- An attacker performs a DNS spoofing or BGP hijacking attack to intercept the `raw.githubusercontent.com` or `github.com` requests. Because the `--no-check-certificate` flag is present, `wget` accepts the attacker's self-signed or invalid certificate. The attacker serves a malicious bash script containing a reverse shell, ransomware, or a botnet client. The AI agent or user then makes the script executable and runs it, granting the attacker full root access to the VPS.

### Recommendations:
- [security_analyst] This file poses no direct security threat as a skill prompt or documentation file. No prompt injection or malicious instructions are present. If this skill is intended to be executed automatically by an AI agent, flag the use of --no-check-certificate and remote script execution for human review. Consider adding a disclaimer about the legal status of circumvention tools in various jurisdictions. No immediate action required for the file itself.
- [intent_analyst] Replace these commands with a hardened workflow: remove `--no-check-certificate`; fetch from pinned commit/tag URLs; verify SHA256/GPG signatures; inspect scripts before running; execute first in a sandbox; apply principle of least privilege and configuration management (Ansible/Terraform + vetted scripts); harden Shadowsocks config (strong password/cipher, disable unauthorized access, firewall and rate-limit); log and monitor service activity; document legal/policy approval for proxy use; and maintain a rollback plan for BBR/kernel changes.
- [adversarial_analyst] Remove the `--no-check-certificate` flag from all `wget` commands to enforce strict TLS validation. Additionally, implement integrity checks by verifying the SHA256 hashes of the downloaded scripts before making them executable and running them. Avoid executing unpinned third-party scripts directly without review.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a documentation/skill file describing how to install Shadowsocks (a proxy/VPN tool) and BBR (a TCP congestion control algorithm) on a Vultr VPS. The file itself contains no executable code — it is a Markdown guide with shell command snippets. The commands download and execute shell scripts from GitHub (teddysun's repositories), which is a common but inherently risky pattern ('curl | bash' style). The tool's purpose — setting up a circumvention proxy — is legal in many jurisdictions but restricted or illegal in others (notably China, where the Chinese text suggests the target audience). There are no prompt injection attempts, jailbreak backdoors, obfuscated payloads, or data exfiltration instructions in this file.

**intent_analyst** (openai/gpt-5.3-codex):
> This document is an operational guide for setting up a Vultr VPS with Shadowsocks (a proxy service) and BBR (TCP congestion control tuning) by downloading and executing third-party shell scripts. The likely intent is to improve network performance and enable proxy-based access, which can be legitimate (privacy, remote access, bypassing restrictive networks in lawful contexts) but is dual-use. The main risk is not obvious malware in the text itself, but unsafe installation practice: executing remote scripts as root, with certificate checks explicitly disabled, and no integrity verification.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> From an attacker's perspective, this skill provides a prime opportunity for a Man-in-the-Middle (MitM) attack. The use of `wget --no-check-certificate` completely disables SSL/TLS validation when downloading shell scripts from GitHub. If an AI agent or user executes these commands, an attacker who can intercept the network traffic (e.g., via DNS spoofing or compromised routing) can serve a malicious payload instead of the legitimate Shadowsocks/BBR scripts. Since installing BBR and Shadowsocks typically requires root privileges, the malicious script would likely be executed as root, leading to a complete system compromise.

#### Disagreements Between Models

- **security_analyst**: LOW - This is a documentation/skill file describing how to install Shadowsocks (a proxy/VPN tool) and BBR (a TCP congestion control algorithm) on a Vultr VPS. The file itself contains no executable code — i
- **intent_analyst**: HIGH - This document is an operational guide for setting up a Vultr VPS with Shadowsocks (a proxy service) and BBR (TCP congestion control tuning) by downloading and executing third-party shell scripts. The 
- **adversarial_analyst**: HIGH - From an attacker's perspective, this skill provides a prime opportunity for a Man-in-the-Middle (MitM) attack. The use of `wget --no-check-certificate` completely disables SSL/TLS validation when down

### Analysis Group #2

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

**security_analyst** (anthropic/claude-sonnet-4.6):
> The file being analyzed (system.md) contains only reference links to technical articles about Disruptor (a high-performance queue library) and time wheel algorithms for managing delayed/timeout/scheduled tasks. There is no executable code, no prompt injection, no data exfiltration instructions, no jailbreak backdoors, and no suspicious behavior of any kind. The content is purely educational/reference material with links to legitimate technical blogs.

**intent_analyst** (openai/gpt-5.3-codex):
> The provided content is not executable code but a short knowledge note listing learning resources about the Disruptor pattern and timing wheel design for high-performance queuing and delayed/timeout task scheduling. Its apparent intent is educational/documentation-oriented, pointing readers to performance-engineering concepts rather than performing any action itself.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided file (`system.md`) consists entirely of benign markdown text containing links to technical articles and a YouTube video about software architecture concepts (LMAX Disruptor and Time Wheel algorithms). There are no executable commands, prompt instructions, or malicious payloads. While the context (`SKILL.MD`) describes downloading and executing shell scripts for setting up a proxy (which carries inherent risks if the source repository is compromised), the actual file under review is completely harmless and appears to be a disorganized collection of developer notes.

## Trust Chain Analysis

**Overall Trust Verdict**: ⚠️ **VERIFY_BEFORE_USE**
*The skill's purpose is legitimate, but both scripts are fetched with TLS verification disabled (--no-check-certificate) from unpinned master branch URLs, meaning safety depends entirely on the continued integrity of teddysun's GitHub accounts and the absence of any MITM attacker — all of which must be manually verified before execution, especially given the 2017 authorship date of this skill.*

This skill is a documentation/instruction guide that directs a user or agent to download and execute two shell scripts from external URLs using wget with certificate verification disabled (--no-check-certificate). The sensitive operations are: (1) fetching and executing shadowsocks-all.sh from raw.githubusercontent.com, and (2) fetching and executing bbr.sh from github.com — both as root-level scripts on a VPS. There are no API keys, wallet addresses, or calldata involved. The entire trust chain collapses to a single critical question: are these GitHub URLs serving the legitimate, unmodified scripts from the trusted author 'teddysun'? The use of --no-check-certificate eliminates TLS as a protection layer, meaning a network-level attacker (MITM) could substitute malicious scripts. If the GitHub repositories are compromised, renamed, or the URLs are hijacked, arbitrary code executes with root privileges on the target server. The skill's purpose (setting up a proxy server) is legitimate, but the execution method carries significant supply-chain and MITM risk.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | URL for shadowsocks installation script fetched via wget | `hardcoded` — skill.md — hardcoded to https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh | ta_001, ta_002, ta_003 | 🟠 HIGH |
| API Endpoints | URL for BBR kernel script fetched via wget | `hardcoded` — skill.md — hardcoded to https://github.com/teddysun/across/raw/master/bbr.sh | ta_001, ta_004, ta_003 | 🟠 HIGH |
| User Input | Interactive configuration inputs during shadowsocks-all.sh execution (password, port, encryption method) | `user_provided` — skill.md — the script ./shadowsocks-all.sh is interactive; user provides SS password, port, and cipher at runtime | — | 🟢 LOW |
| Authorization / API Keys | No API keys or credentials are present in this skill | `hardcoded` — skill.md — no credentials, tokens, or secrets appear anywhere in the skill files | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The GitHub repository github.com/teddysun/shadowsocks_install still exists, is owned by the original author, and the current master branch content of shadowsocks-all.sh is safe and unmodified from its last known-good state

**How to verify:** 1. Visit https://github.com/teddysun/shadowsocks_install and check the account owner and commit history. 2. Review the current content of shadowsocks-all.sh on master. 3. Check the commit log for any recent unexpected changes. 4. Cross-reference with the referenced blog post at https://shadowsocks.be/11.html for the expected script behavior.

**If wrong:** ⚠️ A compromised or transferred repo means root-level malicious code executes on the VPS during installation.

#### 2. [🔴 MUST] The GitHub repository github.com/teddysun/across still exists, is owned by the original author, and the current master branch content of bbr.sh is safe

**How to verify:** 1. Visit https://github.com/teddysun/across and verify account ownership and commit history. 2. Review the current content of bbr.sh. 3. Check https://teddysun.com/489.html for the expected script description and compare.

**If wrong:** ⚠️ A malicious bbr.sh could install a backdoored kernel or create persistent root access on the VPS.

#### 3. [🔴 MUST] The --no-check-certificate flag is necessary for these specific downloads (i.e., there is no valid TLS certificate issue with raw.githubusercontent.com or github.com that would require bypassing verification)

**How to verify:** 1. Attempt to run the wget commands WITHOUT --no-check-certificate on a test system. 2. Both raw.githubusercontent.com and github.com have valid, widely-trusted TLS certificates — certificate verification should succeed without this flag.

**If wrong:** ⚠️ The flag is unnecessary and its presence only increases MITM risk. If the flag was added intentionally to bypass a certificate warning (e.g., a spoofed domain), that would indicate the URLs themselves may be malicious.

#### 4. [🟡 SHOULD] The SHA256 checksums of the downloaded scripts match known-good values before execution

**How to verify:** 1. Download the scripts on a trusted network. 2. Run sha256sum shadowsocks-all.sh and sha256sum bbr.sh. 3. Compare against checksums published by teddysun (check teddysun.com or the GitHub release tags if available). 4. Re-verify on the target VPS before executing.

**If wrong:** ⚠️ A modified script (via MITM or repo compromise) would have a different checksum, providing an early warning before execution.

#### 5. [🟡 SHOULD] The referenced blog posts (medium.com/@zoomyale and boblogs.com) still recommend these exact URLs and have not issued security warnings about these scripts

**How to verify:** 1. Visit https://medium.com/@zoomyale/... and http://boblogs.com/index.php/2017/09/14/vultr_ss/ 2. Check if the articles have been updated with warnings or alternative URLs. Note: these are 2017-era articles and may be outdated.

**If wrong:** ⚠️ If the original authors have retracted or updated their recommendations, this skill may be directing users to deprecated or unsafe resources.

#### 6. [🔵 NICE TO HAVE] teddysun.com (the author's official site) still endorses these GitHub repositories as the canonical source for these scripts

**How to verify:** Visit https://teddysun.com/489.html (BBR script page) and https://shadowsocks.be/11.html (shadowsocks script page) and confirm the GitHub URLs match those listed in this skill.

**If wrong:** ⚠️ If the author has moved to different URLs or deprecated these repos, the skill may be pointing to unmaintained or potentially hijacked repositories.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:30:58.168360+00:00*