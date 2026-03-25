# Security Audit Report: wuyumin/VSCode

| Field | Value |
|-------|-------|
| Repository | [wuyumin/VSCode](https://github.com/wuyumin/VSCode) |
| Commit | `ebc5bcee434a` |
| Commit Date | 2026-03-07T15:37:35+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:30:58.332737+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 76% |
| Files Scanned | 1 / 8 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 8 |
| Files analyzed | 1 |
| Skipped (unsupported type) | 7 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 1 |
| Dominant language | Markdown (✅ supported) |
| Top file types | .md(6), (no ext)(1), .code-snippets(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 1 |
| 🔵 LOW | 0 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 1 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **The SSH agent workflow described (ssh-agent + ssh-add) is the standard, secure method for managing SSH key passphrases on Unix/Windows systems** *(confidence: 30%)*
   - 检验结果：Insufficient data for ssh-agent / ssh-add system utilities — manual check recommended.
   - 关联主体：`ssh-agent / ssh-add system utilities`

2. ❌ **https://code.visualstudio.com is the official Microsoft VSCode download and documentation site** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for code.visualstudio.com. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`code.visualstudio.com`

3. ✅ **https://github.com/coder/code-server is the legitimate open-source code-server project maintained by Coder** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/coder/code-server`
   - 证据：[Trusted service whitelist policy](https://github.com/)

4. ❌ **code is a reputable, widely-used service** *(confidence: 30%)*
   - 检验结果：Insufficient data for code — manual check recommended.
   - 关联主体：`code`

## Detailed Findings

### 🟡 Finding #1: Skill Prompt: prompt_embedded_shell

- **File**: `Skill.md`
- **Line**: 6
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Shell code block with dangerous commands embedded in skill instructions

```
       3 | ### 如何使用有私钥密码的ssh协议的git？
       4 | 
       5 | vi sshagent.sh
>>>    6 | ```bash
       7 | eval $(ssh-agent -s) > /dev/null
       8 | ssh-add ~/.ssh/id_ed25519
       9 | # 查看所有ssh-add -l
      10 | # 删除单个ssh-add -d ~/.ssh/id_ed25519 删除所有ssh-add -D
```

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 76%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file serves as documentation for VSCode users explaining how to use SSH keys with Git. The bash snippet is a tutorial example showing how to start an SSH agent and add a private key, which is a standard workflow for developers using SSH-based Git authentication.
- The safe/correct pattern is: (1) start a trusted ssh-agent process in your own user session, (2) add only the intended private key (for example ~/.ssh/id_ed25519), (3) verify loaded identities with `ssh-add -l`, and (4) launch VSCode from that same shell so `SSH_AUTH_SOCK` is inherited. For safer operation, use a key with a passphrase, restrict key file permissions (`chmod 600 ~/.ssh/id_ed25519`), and optionally set key lifetime (`ssh-add -t 1h ~/.ssh/id_ed25519`) so credentials are not kept indefinitely. The `eval "$(ssh-agent -s)"` pattern is common and acceptable when `ssh-agent` comes from a trusted system path and the environment is not compromised.
- This is a tutorial/guide for developers to streamline their Git workflow in VSCode by caching their SSH key passphrases locally using `ssh-agent`.

### Why this might be dangerous:
- N/A - The commands shown are standard SSH agent management commands presented in a documentation context. While ssh-add accesses ~/.ssh/id_ed25519, this is the expected and documented behavior of SSH key management. There is no mechanism for automated execution or data exfiltration.
- This becomes dangerous under specific conditions: if `ssh-agent` is replaced/hijacked in PATH (or run from an untrusted location), `eval $(ssh-agent -s)` can execute attacker-controlled shell code. It is also risky on shared/multi-user systems if agent sockets are exposed or permissions are weak, because other local users/processes may use your loaded key while the agent is active. Long-lived agents with unlocked keys increase blast radius after endpoint compromise. Agent forwarding to untrusted hosts can also let remote systems request signatures from your local agent. So the gray area is not the feature itself, but the combination of `eval`, trust boundary assumptions, socket exposure, and session hygiene.
- As a static Markdown file, this text cannot be directly abused. An attack scenario would only exist if a poorly designed automated AI agent or script blindly parsed and executed bash blocks from this file without user consent. However, even in that highly unlikely scenario, the commands only start a local SSH agent and prompt to add a local key, which does not compromise the system or exfiltrate data.

### Recommendations:
- [security_analyst] No action required. This is legitimate documentation content. The static analysis false positive was triggered by the presence of shell commands referencing ~/.ssh paths, but in context these are clearly instructional examples, not malicious payloads.
- [intent_analyst] Keep this guidance, but harden it: use `eval "$(/usr/bin/ssh-agent -s)"` (or your system’s trusted absolute path), verify `command -v ssh-agent`, and avoid running in privileged/root shells unnecessarily. Add explicit safeguards in docs: check key permissions, set TTL with `ssh-add -t`, disable or tightly control agent forwarding, and kill agent when done (`ssh-agent -k`) on shared machines. In VSCode docs, clarify that users should only run these commands in trusted local terminals and avoid copying scripts from untrusted sources.
- [adversarial_analyst] Mark the static analysis finding as a false positive. No changes are required as the file is purely informational documentation.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a VSCode tips and tricks documentation file (Skill.md) written in Chinese. The shell code block in question is a standard, well-documented example of how to configure SSH agent for use with Git in VSCode. The commands shown (ssh-agent, ssh-add) are completely standard SSH key management utilities, and the script is explicitly presented as instructional content for users to manually create and run. There is no obfuscation, no data exfiltration, no hardcoded external URLs, and no unexpected resource access. This is exactly the kind of content expected in a VSCode configuration/tips repository.

**intent_analyst** (openai/gpt-5.3-codex):
> This markdown snippet is trying to help users configure Git-over-SSH in VSCode when their private key is passphrase-protected. The core intent is legitimate: start an SSH agent, add a key, and make VSCode inherit agent environment variables so Git operations work without repeatedly prompting for the key password.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard Markdown documentation file explaining how to configure `ssh-agent` for use with VSCode and Git. The flagged bash commands (`eval $(ssh-agent -s)` and `ssh-add`) are universally accepted, legitimate methods for initializing an SSH agent and adding a private key to avoid repeatedly typing passphrases. Evaluating this as a 'skill_prompt', there are absolutely no prompt injection vectors, jailbreak backdoors, or data exfiltration instructions present. The static analysis finding is a false positive triggered simply by the presence of shell commands inside a markdown code block.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a VSCode tips and tricks documentation file (Skill.md) written in Chinese. The shell code block in question is a standard, well-documented example of how to configure SSH agent for use with Gi
- **intent_analyst**: LOW - This markdown snippet is trying to help users configure Git-over-SSH in VSCode when their private key is passphrase-protected. The core intent is legitimate: start an SSH agent, add a key, and make VS
- **adversarial_analyst**: SAFE - The provided code is a standard Markdown documentation file explaining how to configure `ssh-agent` for use with VSCode and Git. The flagged bash commands (`eval $(ssh-agent -s)` and `ssh-add`) are un

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill is purely static instructional documentation with no automated code execution, no API calls, no credential handling, and no dynamic data flows — the only trust dependencies are that the referenced URLs (code.visualstudio.com, github.com/coder/code-server) are legitimate and that the documented SSH agent workflow follows standard secure practices, both of which are easily verifiable and highly likely to be true.*

This skill is a static documentation/tutorial resource about VSCode configuration tips, SSH agent setup for Git, and workspace management. It contains no executable code, no API calls, no credential handling, no blockchain interactions, and no dynamic data flows. The only 'sensitive' content is instructional guidance on how a user should manually configure SSH keys and run SSH agent commands on their own machine. There are no automated operations performed by this skill — all actions described require explicit, manual user execution. The trust chain is essentially flat: the skill presents static text, and the user decides whether to follow the instructions. The primary risk surface is whether the documented commands and workflows are accurate and safe for users to follow.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| User Input | SSH private key path provided by the user when running ssh-add commands | `user_provided` — Skill.md — instructions reference '~/.ssh/id_ed25519' as a default path, but the user supplies the actual path and passphrase manually | ta_001 | 🟢 LOW |
| API Endpoints | External URLs referenced in documentation (VSCode official site, code-server GitHub) | `hardcoded` — README.md — 'https://code.visualstudio.com' and 'https://github.com/coder/code-server' are hardcoded reference links | ta_002, ta_003 | 🟢 LOW |
| Authorization | SSH private key passphrase handling via ssh-agent | `user_provided` — Skill.md — instructions tell the user to run 'ssh-add ~/.ssh/id_ed25519' and enter their passphrase interactively | ta_001 | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🟡 SHOULD] https://code.visualstudio.com is the official Microsoft-owned domain for VSCode downloads

**How to verify:** Check WHOIS records for code.visualstudio.com to confirm Microsoft ownership; cross-reference with https://microsoft.com and official Microsoft documentation

**If wrong:** ⚠️ Users directed to a spoofed domain could download malware disguised as VSCode

#### 2. [🟡 SHOULD] github.com/coder/code-server is the legitimate, actively maintained open-source project for browser-based VSCode

**How to verify:** Visit https://github.com/coder/code-server and verify the repository is owned by the 'coder' organization, check for verified commits, star count, and cross-reference with https://coder.com official website

**If wrong:** ⚠️ Users could install a compromised or abandoned server-side VSCode instance

#### 3. [🔵 NICE TO HAVE] The ssh-agent and ssh-add commands described work correctly on both Windows (start-ssh-agent) and Unix systems as documented

**How to verify:** Cross-reference with official OpenSSH documentation (https://www.openssh.com/manual.html) and Microsoft's SSH documentation for Windows (https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement)

**If wrong:** ⚠️ Users could misconfigure their SSH agent, either failing to authenticate or inadvertently exposing key material

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:30:58.332737+00:00*