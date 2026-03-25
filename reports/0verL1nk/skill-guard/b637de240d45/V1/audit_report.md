# Security Audit Report: 0verL1nk/skill-guard

| Field | Value |
|-------|-------|
| Repository | [0verL1nk/skill-guard](https://github.com/0verL1nk/skill-guard) |
| Commit | `b637de240d45` |
| Commit Date | 2026-02-09T10:18:45+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:27:52.597172+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 84% |
| Files Scanned | 27 / 36 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 36 |
| Files analyzed | 27 |
| Skipped (unsupported type) | 9 |
| Skipped (too large) | 0 |
| Analyzed by language | shell: 2, tool_config: 12, dependency: 5, javascript: 8 |
| Dominant language | JSON (✅ supported) |
| Code coverage | 22/27 code files (81%) |
| Top file types | .json(15), .md(6), .js(4), .ts(4), (no ext)(2), .sh(2), .yml(2), .mts(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 7 |
| 🔵 LOW | 4 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 6 |

## Detailed Findings

### 🟡 Finding #1: Dangerous pattern: fs write/delete

- **File**: `packages\cli\src\index.ts`
- **Line**: 42
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
      37 |   .description("Generate a new Ed25519 keypair")
      38 |   .option("-o, --out <dir>", "Output directory", ".")
      39 |   .action(async (options) => {
      40 |     const keys = generateKeyPair();
      41 |     await fs.ensureDir(options.out);
>>>   42 |     await fs.writeFile(path.join(options.out, "private.key"), keys.secretKey);
      43 |     await fs.writeFile(path.join(options.out, "public.key"), keys.publicKey);
      44 |     console.log("✅ Keys generated!");
      45 |     console.log(`Public Key: ${keys.publicKey}`);
      46 |   });
      47 | 
```

### 🟡 Finding #2: Dangerous pattern: fs write/delete

- **File**: `packages\cli\src\index.ts`
- **Line**: 43
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
      38 |   .option("-o, --out <dir>", "Output directory", ".")
      39 |   .action(async (options) => {
      40 |     const keys = generateKeyPair();
      41 |     await fs.ensureDir(options.out);
      42 |     await fs.writeFile(path.join(options.out, "private.key"), keys.secretKey);
>>>   43 |     await fs.writeFile(path.join(options.out, "public.key"), keys.publicKey);
      44 |     console.log("✅ Keys generated!");
      45 |     console.log(`Public Key: ${keys.publicKey}`);
      46 |   });
      47 | 
      48 | program.command("sign")
```

### 🟡 Finding #3: Dangerous pattern: fs write/delete

- **File**: `packages\cli\src\index.ts`
- **Line**: 214
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
     209 |               console.log(`   Fetching ${url}...`);
     210 |               // Use global fetch (Node 18+)
     211 |               const response = await fetch(url);
     212 |               if (!response.ok) throw new Error(`Failed to fetch ${url}: ${response.statusText}`);
     213 |               const text = await response.text();
>>>  214 |               await fs.writeFile(path.join(hooksDir, file), text);
     215 |               console.log(`   - Installed ${file}`);
     216 |           }
     217 | 
     218 |           // Create HOOK.md
     219 |           const hookMd = `
```

### 🟡 Finding #4: Dangerous pattern: fs write/delete

- **File**: `packages\cli\src\index.ts`
- **Line**: 228
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
     223 | ---
     224 | # SkillGuard Hook
     225 | 
     226 | Automatically verifies skills against their manifest.json on startup.
     227 | `;
>>>  228 |           await fs.writeFile(path.join(hooksDir, "HOOK.md"), hookMd.trim());
     229 |           console.log("   - Installed HOOK.md");
     230 | 
     231 |           console.log("\n✅ SkillGuard Hook installed successfully!");
     232 |           console.log("🔄 Please restart your OpenClaw Gateway to apply changes:");
     233 |           console.log("   openclaw gateway restart");
```

### 🟡 Finding #5: Dangerous pattern: network fetch

- **File**: `packages\cli\src\index.ts`
- **Line**: 210
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
     205 |           const files = ["handler.js"];
     206 | 
     207 |           for (const file of files) {
     208 |               const url = `${baseUrl}/${file}`;
     209 |               console.log(`   Fetching ${url}...`);
>>>  210 |               // Use global fetch (Node 18+)
     211 |               const response = await fetch(url);
     212 |               if (!response.ok) throw new Error(`Failed to fetch ${url}: ${response.statusText}`);
     213 |               const text = await response.text();
     214 |               await fs.writeFile(path.join(hooksDir, file), text);
     215 |               console.log(`   - Installed ${file}`);
```

### 🟡 Finding #6: Dangerous pattern: network fetch

- **File**: `packages\cli\src\index.ts`
- **Line**: 211
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
     206 | 
     207 |           for (const file of files) {
     208 |               const url = `${baseUrl}/${file}`;
     209 |               console.log(`   Fetching ${url}...`);
     210 |               // Use global fetch (Node 18+)
>>>  211 |               const response = await fetch(url);
     212 |               if (!response.ok) throw new Error(`Failed to fetch ${url}: ${response.statusText}`);
     213 |               const text = await response.text();
     214 |               await fs.writeFile(path.join(hooksDir, file), text);
     215 |               console.log(`   - Installed ${file}`);
     216 |           }
```

### 🔵 Finding #7: Shell: system_info

- **File**: `demo-did.sh`
- **Line**: 20
- **Severity**: LOW
- **Source**: static
- **Description**: Collects system information [×4 occurrences at lines: 20, 21, 24, 25]

```
--- Line 20 ---
      15 | # Only rebuild core for speed
      16 | npx turbo run build --filter=@skill-guard/core
      17 | 
      18 | echo ""
      19 | echo "📝 Step 4: Signing skill..."
>>>   20 | echo "# DID Demo Skill" > did-skill.md
      21 | node packages/cli/dist/index.js sign did-skill.md --key ./keys/private.key --perms "net.fetch"
      22 | 
      23 | echo ""
      24 | echo "🌐 Step 5: Verifying with DID (did:web:github.com:0verL1nk)..."
      25 | node packages/cli/dist/index.js verify did-skill.md manifest.json --did did:web:github.com:0verL1nk

--- Line 21 ---
      16 | npx turbo run build --filter=@skill-guard/core
      17 | 
      18 | echo ""
      19 | echo "📝 Step 4: Signing skill..."
      20 | echo "# DID Demo Skill" > did-skill.md
>>>   21 | node packages/cli/dist/index.js sign did-skill.md --key ./keys/private.key --perms "net.fetch"
      22 | 
      23 | echo ""
      24 | echo "🌐 Step 5: Verifying with DID (did:web:github.com:0verL1nk)..."
      25 | node packages/cli/dist/index.js verify did-skill.md manifest.json --did did:web:github.com:0verL1nk
      26 | 

--- Line 24 ---
      19 | echo "📝 Step 4: Signing skill..."
      20 | echo "# DID Demo Skill" > did-skill.md
      21 | node packages/cli/dist/index.js sign did-skill.md --key ./keys/private.key --perms "net.fetch"
      22 | 
      23 | echo ""
>>>   24 | echo "🌐 Step 5: Verifying with DID (did:web:github.com:0verL1nk)..."
      25 | node packages/cli/dist/index.js verify did-skill.md manifest.json --did did:web:github.com:0verL1nk
      26 | 
      27 | echo ""
      28 | echo "✨ DID Verification Success!"

--- Line 25 ---
      20 | echo "# DID Demo Skill" > did-skill.md
      21 | node packages/cli/dist/index.js sign did-skill.md --key ./keys/private.key --perms "net.fetch"
      22 | 
      23 | echo ""
      24 | echo "🌐 Step 5: Verifying with DID (did:web:github.com:0verL1nk)..."
>>>   25 | node packages/cli/dist/index.js verify did-skill.md manifest.json --did did:web:github.com:0verL1nk
      26 | 
      27 | echo ""
      28 | echo "✨ DID Verification Success!"
```

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: LOW
- **Confidence**: 70%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This script is a developer-facing demo that walks through the full SkillGuard workflow: key generation, DID resolver configuration, project build, skill signing, and DID-based verification. It is intended to be run locally in a development/testing environment to showcase the tool's capabilities.
- The safe/correct use of this pattern is in an isolated development/demo workspace where source mutation is explicitly expected and disposable. Specifically: run only on a clean throwaway branch or temp clone; ensure the generated key material format is strictly validated before interpolation; perform deterministic file edits via AST/tooling or exact marker replacement (not broad regex); create backups and restore files after the demo; and keep outputs (`did-skill.md`, `manifest.json`, `keys/`) in a temporary directory. For shell hardening, use `set -euo pipefail`, quote all variables, verify required files exist before editing/building, and assert the post-edit diff matches exactly one intended change.
- This is a demo script intended to show users how the DID verification feature works locally without requiring them to set up and host a real DID document on a web server.

### Why this might be dangerous:
- The `sed -i` command injects the generated public key into TypeScript source code. If an attacker could influence the content of `./keys/public.key` (e.g., via a compromised keygen binary or path manipulation), they could inject arbitrary content into the source file. However, this is a low-risk scenario in practice since the key is generated by the tool itself in the same script execution, and the script is a demo not intended for production use.
- This becomes dangerous when executed in a shared CI/CD or production-adjacent repo, or when inputs are not fully trusted. The critical gray-area risk is the in-place `sed` rewrite of `packages/core/src/index.ts`: if replacement matching is too broad, it may silently alter unintended code paths; if interpolated data contains unexpected characters, it can break syntax or inject logic; and because no revert is performed, the mutated resolver logic can be committed and shipped, undermining trust guarantees. Additional risk conditions include running with elevated permissions, reusing generated keys insecurely, and relying on the script output as proof of cryptographic verification while actually testing against a locally patched resolver (false sense of security).
- There is no direct abuse scenario for this script. If an attacker has the ability to execute this script, they already have shell access to the system. The only minor operational risk is that the `sed` command modifies source code (`packages/core/src/index.ts`), which could accidentally be committed by a developer, but since it's modifying a mock resolver, the security impact is negligible.

### Recommendations:
- [security_analyst] This script is acceptable for a demo/development context. Consider adding input sanitization or quoting around the `$PUBKEY` variable in the `sed` command to prevent potential injection if the script is ever adapted for broader use. The static analysis findings are false positives and can be suppressed. No malicious behavior is present.
- [intent_analyst] Refactor the demo to avoid editing source code in place. Prefer dependency injection/config (e.g., env var or test resolver file) so DID key mapping is runtime-configured, not hardcoded mutation. If file patching is unavoidable, use a scoped placeholder token and robust patch tool, validate `PUBKEY` against a strict regex, back up and auto-restore modified files (`trap` on exit), and fail if more/less than one replacement occurs. Run in `mktemp` workspace, add `set -euo pipefail`, check command exit statuses explicitly, and write outputs to a dedicated demo artifact directory. Also clearly label this as a non-production demo flow and block execution in CI/release contexts.
- [adversarial_analyst] Consider using environment variables, dependency injection, or a configuration file to pass the mock public key to the DID resolver instead of modifying source code with `sed` and rebuilding. This avoids dirtying the git working tree and makes the demo cleaner.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a demo shell script for the SkillGuard project that demonstrates the DID (Decentralized Identifier) verification workflow. It generates cryptographic keys, patches a hardcoded public key into source code via sed, rebuilds the project, signs a demo skill file, and verifies it. The script is entirely consistent with its documented purpose as a demonstration tool. The static analysis flags for 'system_info collection' are false positives — the flagged lines are simply echo statements and CLI invocations that are part of the demo workflow. The one noteworthy concern is the use of `sed -i` to modify source code in-place (packages/core/src/index.ts) to inject a dynamically generated public key, which is a code-patching pattern that could be risky if the PUBKEY variable were attacker-controlled, but in this context it is generated locally by the tool itself and is part of a demo script meant to be run in a development environment.

**intent_analyst** (openai/gpt-5.3-codex):
> This script appears to be a local developer demo that exercises the full SkillGuard DID flow: generate a keypair, inject the generated public key into a mock DID resolver implementation, rebuild core, sign a sample skill, and verify it against a DID. The intent is legitimate testing/demo automation, not attack behavior, but it uses unsafe mutation patterns (in-place `sed` source rewrite and broad replacement) that can accidentally corrupt code or be misused in less trusted environments.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The script is a legitimate demonstration of the SkillGuard DID verification process. It generates a keypair, temporarily modifies the source code to mock a DID resolver with the generated public key, rebuilds the core, and then signs and verifies a dummy skill. From an attacker's perspective, there are no exploitable vulnerabilities here as the inputs are controlled by the script itself and it is clearly intended for local testing and demonstration purposes.

#### Disagreements Between Models

- **security_analyst**: LOW - This is a demo shell script for the SkillGuard project that demonstrates the DID (Decentralized Identifier) verification workflow. It generates cryptographic keys, patches a hardcoded public key into 
- **intent_analyst**: MEDIUM - This script appears to be a local developer demo that exercises the full SkillGuard DID flow: generate a keypair, inject the generated public key into a mock DID resolver implementation, rebuild core,
- **adversarial_analyst**: SAFE - The script is a legitimate demonstration of the SkillGuard DID verification process. It generates a keypair, temporarily modifies the source code to mock a DID resolver with the generated public key, 

### Analysis Group #2

- **Final Severity**: LOW
- **Confidence**: 76%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file is used as a starting template for configuring SkillGuard's trust policy. Users replace the placeholder with their actual Ed25519 public key, set appropriate permission boundaries, and optionally enforce version matching. It is consumed by the SkillGuard runtime to validate which skills are trusted and what permissions they are allowed to request.
- Correct/safe usage is to treat this as a starter policy and immediately replace `REPLACE_WITH_YOUR_PUBLIC_KEY` with one or more real, verified Ed25519 public keys under your control (or from explicitly approved publishers), keep `maxPermissions` as restrictive as possible (principle of least privilege), and enable strict controls such as version enforcement where practical. A safe pattern is: (1) maintain a curated key allowlist with provenance checks, (2) define environment-specific permission ceilings (e.g., read-only for local docs, no network by default), (3) set `enforceVersionMatch` to `true` in production to prevent drift/replay/substitution issues, and (4) audit policy changes via code review and signed commits.
- This is clearly an example configuration template provided to users to demonstrate the schema of a policy file. Setting version enforcement to false might be intended to reduce friction during local development, debugging, or initial setup.

### Why this might be dangerous:
- This becomes dangerous if deployed without replacing placeholders, with overly broad permissions, or with relaxed validation semantics. Specific risky conditions include: trusting attacker-controlled keys (or accidentally trusting none and falling back to insecure behavior in downstream code), allowing high-impact permissions in `maxPermissions` (e.g., file write, network fetch, command execution if supported), and keeping `enforceVersionMatch` disabled so a valid signature from one version may be reused against unintended content/version workflows. If the surrounding verifier fails open on policy errors, an attacker could introduce tampered or downgraded skills that still appear acceptable under weak policy constraints.
- Downgrade Attack: Suppose a developer releases v1.0 of a skill with a severe prompt injection vulnerability. They realize the mistake, patch it, and release v1.1, signing it with their key. An attacker who gains limited access to the local file system (or intercepts the skill download) replaces the v1.1 SKILL.md and manifest.json with the vulnerable v1.0 files. Because v1.0 was signed by a key in 'trustedKeys' and 'enforceVersionMatch' is false, SkillGuard validates the files as trusted. The attacker can now exploit the v1.0 vulnerability.

### Recommendations:
- [security_analyst] No action required. This is a benign example configuration file. Users should ensure they replace 'REPLACE_WITH_YOUR_PUBLIC_KEY' with a real, controlled public key before deploying in production, and should consider setting 'enforceVersionMatch' to true for stricter integrity guarantees.
- [intent_analyst] Use this only as a template: replace placeholder keys before use, set `enforceVersionMatch` to `true` for production, and explicitly deny all non-required permissions. Add safeguards that are not present here: schema validation (reject placeholder/default values), fail-closed behavior when policy is invalid or empty, mandatory key rotation/expiration metadata, environment-specific policy profiles, and CI checks that block deployment when trust keys are unset or version enforcement is disabled.
- [adversarial_analyst] Change the example to '"enforceVersionMatch": true' to encourage secure-by-default configurations in production. Add prominent documentation warning users about the risks of downgrade/rollback attacks if they choose to disable version enforcement.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a straightforward example policy configuration file for the SkillGuard tool. It defines a trusted public keys list (with a placeholder value), restricts maximum permissions to 'fs.read', and disables version enforcement. The file is clearly a template/example meant to be customized by users, as indicated by the 'REPLACE_WITH_YOUR_PUBLIC_KEY' placeholder. There is nothing malicious or unexpected here — it is exactly what a security policy configuration file for an agent skill verification system should look like.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is an example trust policy template for SkillGuard, intended to define which public keys are trusted, what maximum permissions signed skills may request, and whether strict version matching is enforced. Its intent is defensive (restricting skill execution to trusted signers and limited permissions), but as written it is a placeholder and includes a potentially risky default (`enforceVersionMatch: false`) that makes it a gray-area configuration depending on how it is used in production.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> While this is a benign example configuration file, it contains a potentially dangerous default setting from an attacker's perspective: '"enforceVersionMatch": false'. In cryptographic signing systems, failing to enforce version matching opens the door to downgrade (or rollback) attacks. An attacker cannot forge a signature for new malicious code, but they could replace a newly patched, secure skill with an older, vulnerable version of that same skill that was legitimately signed in the past. Since the signature is valid and the version isn't checked, the system would execute the vulnerable code.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a straightforward example policy configuration file for the SkillGuard tool. It defines a trusted public keys list (with a placeholder value), restricts maximum permissions to 'fs.read', and d
- **intent_analyst**: LOW - This file is an example trust policy template for SkillGuard, intended to define which public keys are trusted, what maximum permissions signed skills may request, and whether strict version matching 
- **adversarial_analyst**: LOW - While this is a benign example configuration file, it contains a potentially dangerous default setting from an attacker's perspective: '"enforceVersionMatch": false'. In cryptographic signing systems,

### Analysis Group #3

- **Final Severity**: SAFE
- **Confidence**: 77%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This manifest.json is generated by the 'sg sign' CLI command and is used by the SkillGuard verification system to cryptographically verify that a skill file (demo-skill.md) has not been tampered with since it was signed. The public key, hash, and signature together form a verifiable attestation of the skill's integrity and authorship.
- Correct/safe usage is to treat this manifest as a cryptographic attestation that must be independently verified at runtime: (1) load `demo-skill.md` as raw bytes, (2) recompute its hash and compare to `integrity.hash`, (3) verify `signature.value` over a canonical, signed payload using Ed25519 and a pre-trusted public key (not just whatever key is in the manifest), and (4) enforce the declared permissions (`fs.read`) through sandbox/policy controls. Safe implementations also canonicalize JSON before signing/verifying, validate schema/version fields, ensure `integrity.file` resolves to an expected path inside the skill directory, and reject mismatches strictly.
- This is a standard configuration file generated by the SkillGuard CLI to verify the integrity, permissions, and origin of an AI agent skill (`demo-skill.md`).

### Why this might be dangerous:
- This pattern becomes dangerous under specific conditions: if the verifier blindly trusts the manifest’s embedded `author.publicKey`, an attacker can sign malicious content with their own key and still pass verification (self-signed trust problem). If permission declarations are informational only and not actually enforced, malicious skills can exceed `fs.read` and still run. If only `demo-skill.md` is hashed/signed but helper files are loaded dynamically, attackers can tamper unsigned files while preserving a “valid” manifest. If canonicalization is inconsistent, signature checks may be bypassed or misvalidated. If path checks are weak, `integrity.file` could be abused for traversal or file confusion. Missing freshness/revocation handling can also allow replay/downgrade of old but validly signed manifests.
- While this specific file is benign, an attacker could theoretically modify a `manifest.json` file to exploit vulnerabilities in the tool that parses it. For example, if the verification tool does not sanitize the `"file"` field, an attacker could change it to `"../../../etc/passwd"` to attempt a path traversal attack, or manipulate the `"permissions"` array to escalate privileges if the signature verification is bypassed or flawed.

### Recommendations:
- [security_analyst] No action required. This file is functioning exactly as designed. One minor observation: the author name is 'unknown', which reduces the accountability value of the signing system, but this is a usage/policy concern rather than a security vulnerability in the file itself.
- [intent_analyst] Keep this format, but harden verification: pin trusted author keys (or certificate chain) outside the manifest; require canonical signing payload definitions; sign the full skill bundle (or Merkle/file list) rather than a single file; enforce permissions at runtime with deny-by-default policy; validate path confinement for `integrity.file`; add key IDs, revocation support, and optional expiry checks; and fail closed on any verification, schema, or policy mismatch.
- [adversarial_analyst] Ensure that the tool parsing this `manifest.json` properly sanitizes all inputs, especially the `"file"` path to prevent directory traversal attacks. Additionally, ensure robust cryptographic verification is enforced before trusting the `"permissions"` or `"integrity"` fields.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard manifest.json file for the SkillGuard tool, which is a cryptographic signing and verification system for AI agent skills. The file contains expected metadata fields: a schema version, skill name, version, author information with a public key, declared permissions (fs.read only), an integrity hash of the associated skill file, and an Ed25519 signature with timestamp. All fields are consistent with the documented purpose of the tool — establishing a chain of trust for AI agent capabilities. There is nothing unexpected, obfuscated, or suspicious in this configuration file.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is a security manifest, not executable logic: it appears to describe a signed skill artifact (`demo-skill.md`) with declared permission scope (`fs.read`), content hash, and Ed25519 signature metadata. The likely intent is integrity and provenance verification before loading a skill. By itself this is legitimate, but it is a gray area because safety depends entirely on how verifiers validate trust, enforce permissions, and bind the signature to the correct files.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a static JSON manifest file used by the SkillGuard tool to store cryptographic signatures and integrity hashes for a demo skill. It contains no executable code, prompt instructions, or inherently dangerous configurations. While an attacker might try to manipulate fields like the file path or signature in a real-world scenario to exploit vulnerabilities in the parsing/verification logic, this specific file contains standard, benign values.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard manifest.json file for the SkillGuard tool, which is a cryptographic signing and verification system for AI agent skills. The file contains expected metadata fields: a schema versio
- **intent_analyst**: LOW - This file is a security manifest, not executable logic: it appears to describe a signed skill artifact (`demo-skill.md`) with declared permission scope (`fs.read`), content hash, and Ed25519 signature
- **adversarial_analyst**: SAFE - The provided code is a static JSON manifest file used by the SkillGuard tool to store cryptographic signatures and integrity hashes for a demo skill. It contains no executable code, prompt instruction

### Analysis Group #4

- **Final Severity**: SAFE
- **Confidence**: 78%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This package-lock.json is used by npm to ensure reproducible installs of the project's development dependencies. The esbuild packages are platform-specific optional binaries used for fast JavaScript bundling, turbo is used for monorepo task orchestration, and typescript provides type checking. This is standard tooling for a modern TypeScript/Node.js project.
- Correct/safe usage of this pattern is to commit the lockfile to version control, use `npm ci` in CI/CD for deterministic installs, and treat lockfile diffs as security-sensitive changes. It is normal for lockfiles to include many platform-specific optional packages (like `@esbuild/linux-*`, `darwin-*`, etc.) and `integrity` hashes. Safe workflow includes: (1) generating lockfiles only from trusted registries, (2) reviewing unexpected dependency additions/version jumps, (3) enabling automated vulnerability scanning (`npm audit`, SCA tools), and (4) enforcing branch protection so lockfile changes are code-reviewed.
- This file is automatically generated by npm to lock down dependency versions, ensuring reproducible builds across different environments. The `@esbuild` packages are standard dependencies for modern JavaScript/TypeScript build tools (like Turbo, which is listed in the devDependencies).

### Why this might be dangerous:
- This becomes dangerous when teams assume lockfiles are always safe and auto-merge dependency updates without review. Specific risky conditions include: a compromised npm package version being added to the lockfile, lockfile poisoning via malicious PR that introduces unexpected transitive dependencies, registry/DNS/proxy tampering in environments that do not verify integrity correctly, or developers running `npm install` (which may rewrite lockfiles) from untrusted branches. Even with integrity hashes, if the lockfile itself is attacker-controlled or trusted keys/registry are compromised, malicious code can still enter at install/runtime through dependency scripts or package contents.
- While this specific snippet is safe, attackers often target `package.json` and `package-lock.json` files to execute supply chain attacks. They might introduce malicious dependencies via typosquatting (e.g., `@esbuild/linux-arm64` vs `@esbuild-linux-arm64`) or dependency confusion. If an attacker gained commit access, they could subtly alter the `resolved` URL or `integrity` hash to point to a compromised package.

### Recommendations:
- [security_analyst] No action required. The lockfile appears legitimate with all packages resolved from the official npm registry with integrity hashes. Standard practice would be to periodically audit dependencies with 'npm audit' and keep them updated, but no security issues are present here.
- [intent_analyst] Keep using a lockfile, but harden the process: require human review for all lockfile diffs, enforce `npm ci` (not `npm install`) in CI, pin and verify trusted registry settings, enable Dependabot/Renovate with approval gates, run continuous SCA/audit scans, and add policy checks that flag unexpected new packages/scopes/postinstall scripts. For high-assurance environments, use signed provenance (e.g., Sigstore/npm provenance), an internal artifact mirror, and allowlist critical dependencies.
- [adversarial_analyst] Continue using standard dependency management practices. Regularly audit dependencies using tools like `npm audit` or automated supply-chain security scanners (like Dependabot or Snyk) to ensure no vulnerable or malicious packages are introduced in the future. Enforce branch protection and code reviews to prevent unauthorized modifications to lockfiles.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard npm package-lock.json file for the skill-guard-monorepo project. It contains locked dependency versions for development tools including esbuild (a JavaScript bundler), turbo (a monorepo build tool), and typescript. All resolved URLs point to the official npm registry (registry.npmjs.org), and the packages listed are well-known, legitimate development dependencies. The integrity hashes are present for supply chain verification. There is nothing unexpected or suspicious in this lockfile — it is exactly what you would expect for a TypeScript monorepo project using turbo for build orchestration.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is a standard npm `package-lock.json` for a TypeScript/Turbo monorepo (`skill-guard-monorepo`) and appears intended to pin exact dependency versions (including platform-specific optional `@esbuild/*` binaries) for reproducible installs. There is no direct executable logic here and no obvious malicious intent in the shown content; the primary security relevance is indirect, through software supply-chain trust in the locked package versions and sources.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard `package-lock.json` snippet containing official `@esbuild` platform-specific binary dependencies. From an attacker's perspective, there are no signs of malicious packages, typosquatting, or dependency confusion attacks in this snippet. It is a completely benign and expected artifact for a Node.js project using modern build tools.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard npm package-lock.json file for the skill-guard-monorepo project. It contains locked dependency versions for development tools including esbuild (a JavaScript bundler), turbo (a mono
- **intent_analyst**: LOW - This file is a standard npm `package-lock.json` for a TypeScript/Turbo monorepo (`skill-guard-monorepo`) and appears intended to pin exact dependency versions (including platform-specific optional `@e
- **adversarial_analyst**: SAFE - The provided code is a standard `package-lock.json` snippet containing official `@esbuild` platform-specific binary dependencies. From an attacker's perspective, there are no signs of malicious packag

### Analysis Group #5

- **Final Severity**: MEDIUM
- **Confidence**: 69%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This configuration file is used by the SkillGuard OpenClaw hook to define which public keys are trusted for skill signature verification, what permissions skills are allowed to request, and whether version numbers must match during verification. It acts as a security policy enforced at gateway startup when scanning the skills directory.
- The safe/correct usage is as a policy gate in environments where you want strict signer pinning and least-privilege permissions. `trustedKeys` should contain only audited publisher keys, rotated via controlled process; `maxPermissions` should be the minimal set required by deployed skills (here, read-only filesystem access); and this file should be treated as immutable deployment config (e.g., committed, reviewed, and integrity-monitored). If `enforceVersionMatch` is intentionally set to `false`, it should only be in controlled compatibility scenarios (e.g., transitional rollout), where signature verification still validates exact content hashes and where other controls ensure only approved versions are present.
- This configuration is used to define a baseline security policy for the SkillGuard system, specifying which developer keys are trusted and capping the maximum allowed permissions to prevent destructive actions. Disabling version matching might be a temporary measure during development, a misconfiguration, or an intentional choice to ensure backward compatibility with older signed skills that haven't been updated to a newer manifest format.

### Why this might be dangerous:
- The `enforceVersionMatch: false` setting could be exploited in a scenario where an attacker replaces a newer, more secure skill with an older, vulnerable version that still carries a valid signature from a trusted key. Since version matching is disabled, the old skill would pass verification despite being outdated. Additionally, if the trusted key listed were compromised, any skill signed with that key would be trusted unconditionally.
- This becomes dangerous if version matching is relied on for release control but disabled here. Specific risk conditions: (1) attacker can introduce an older yet validly signed skill version with known vulnerabilities (rollback attack), (2) policy consumers assume semantic version pinning but runtime accepts mismatched/legacy manifests, (3) trusted key is compromised and there is no key revocation/rotation enforcement, or (4) permission interpretation differs between tooling versions, allowing a manifest that appears to request only `fs.read` to gain broader behavior via parser/version discrepancies. In these conditions, the policy may still report verification success while allowing unsafe code states.
- An attacker discovers that version 1.0 of a skill (signed by the trusted key) has a path traversal vulnerability allowing arbitrary file reads. The developer releases version 1.1 fixing the issue. Because `enforceVersionMatch` is set to `false`, an attacker who gains write access to the `skills/` directory can replace the v1.1 skill files with the older v1.0 files. The SkillGuard system verifies the signature of v1.0 successfully (since it was legitimately signed by the trusted key in the past) and loads it. The attacker then exploits the path traversal vulnerability to read sensitive files from the host system.

### Recommendations:
- [security_analyst] Set `enforceVersionMatch` to `true` to prevent version downgrade/replay attacks. Ensure the trusted key listed is properly protected and consider implementing key rotation procedures. The overall configuration is reasonable for its stated purpose, but the disabled version enforcement is a security gap that should be addressed.
- [intent_analyst] Enable `enforceVersionMatch: true` in production unless there is a documented, time-bounded exception. Add explicit anti-rollback controls (minimum allowed version or exact version pin/hash), implement key rotation and revocation support (not just static allowlist), and enforce policy-file integrity (signed policy or checksum verification at startup). Also add CI checks that fail builds when policy loosens (new permissions, disabled version match, new trusted keys without approval) and runtime logging/alerts for any version mismatch acceptance path.
- [adversarial_analyst] Change `"enforceVersionMatch"` to `true` to prevent downgrade attacks. Additionally, consider implementing a revocation list (CRL) or a minimum required version threshold to ensure that known vulnerable versions of skills cannot be loaded, even if their cryptographic signatures remain mathematically valid.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a policy configuration file for the SkillGuard security tool that defines trusted public keys, maximum allowed permissions, and version enforcement settings. The file itself is not malicious — it is the expected configuration artifact for a skill integrity verification system. However, two minor concerns are worth noting: (1) `enforceVersionMatch` is set to `false`, which weakens the security posture by allowing skills with mismatched versions to pass verification, potentially enabling replay or downgrade attacks; (2) the `maxPermissions` list is restrictive (only `fs.read`), which is actually a positive security control. The hardcoded trusted key is expected for this type of tool and is not inherently suspicious.

**intent_analyst** (openai/gpt-5.3-codex):
> This `strict-policy.json` appears to define a trust policy for SkillGuard verification: only manifests signed by a specific trusted Ed25519 public key are accepted, and skills are limited to `fs.read` permission. The intent is defensive (restrict signer identity and capability scope), but `enforceVersionMatch: false` weakens integrity guarantees by allowing version drift between expected and presented artifacts, making this a security gray area rather than clearly unsafe or malicious code.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> From an attacker's perspective, the most notable feature of this configuration is `"enforceVersionMatch": false`. While the policy correctly restricts permissions to `fs.read` and enforces a specific trusted public key, disabling version matching opens the door to downgrade attacks (rollback attacks). If a previously signed version of a skill contained a vulnerability (e.g., a path traversal flaw in its read operations) and was later patched, an attacker could force the system to use the older, vulnerable, but still cryptographically valid version to exploit the known flaw.

#### Disagreements Between Models

- **security_analyst**: LOW - This is a policy configuration file for the SkillGuard security tool that defines trusted public keys, maximum allowed permissions, and version enforcement settings. The file itself is not malicious —
- **intent_analyst**: MEDIUM - This `strict-policy.json` appears to define a trust policy for SkillGuard verification: only manifests signed by a specific trusted Ed25519 public key are accepted, and skills are limited to `fs.read`
- **adversarial_analyst**: MEDIUM - From an attacker's perspective, the most notable feature of this configuration is `"enforceVersionMatch": false`. While the policy correctly restricts permissions to `fs.read` and enforces a specific 

### Analysis Group #6

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard Turborepo configuration file (turbo.json) that defines build pipeline tasks for a monorepo. It specifies three tasks: 'build' (with upstream dependency ordering and dist output), 'test' (dependent on build), and 'lint'. There is nothing suspicious or malicious here — this is boilerplate Turborepo configuration that is expected for any JavaScript/TypeScript monorepo using Turbo as a build orchestration tool.

**intent_analyst** (openai/gpt-5.3-codex):
> This `turbo.json` is a standard Turborepo task pipeline configuration that defines normal CI/dev workflow behavior: `build` depends on upstream package builds and emits `dist/**`, `test` depends on `build`, and `lint` is declared with default behavior. The intent is to orchestrate monorepo tasks efficiently, not to execute hidden logic or alter security-sensitive behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard, boilerplate `turbo.json` configuration file used by Turborepo to manage build tasks in a monorepo. It simply defines basic pipeline stages (`build`, `test`, `lint`) and their topological dependencies. From an attacker's perspective, this file contains no executable logic, no external network calls, and no complex configurations that could be exploited. It is purely structural metadata for the build system.

### Analysis Group #7

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a standard GitHub Issue template in YAML format for bug reports. It defines a structured form for users to submit bug reports, collecting information such as what happened, reproduction steps, SkillGuard version, and operating system. There is no code execution, no data exfiltration, no obfuscation, and no security concerns whatsoever. It is a completely benign configuration file used by GitHub's issue tracking system.

**intent_analyst** (openai/gpt-5.3-codex):
> This YAML defines a standard GitHub issue template for bug reports, guiding users to provide required fields (what happened, reproduction steps, version, and OS). Its intent is purely organizational: improve bug triage quality and consistency in the repository.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> From an attacker's perspective, this file presents no attack surface. It is a standard GitHub issue template written in YAML. It merely instructs the GitHub web interface on how to render a form for users submitting bug reports. It contains no executable code, does not process runtime data, and cannot be exploited to compromise the application, the AI agent, or its users.

### Analysis Group #8

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a standard GitHub Actions issue template in YAML format for feature requests. It defines a structured form with three textarea fields asking users to describe a problem, a desired solution, and alternatives considered. There is no code execution, no data access, no network requests, and no security-relevant logic whatsoever. It is a completely benign configuration file used to standardize GitHub issue submissions.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> This file is a standard GitHub issue template used to format feature requests on the repository. It contains no executable code, prompt instructions, or configuration that could be exploited by an attacker. It solely dictates the structure of a web form rendered on GitHub.com.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is a standard GitHub Issue Forms template for collecting feature requests. It defines structured fields (problem, solution, alternatives), default labels, and required validations to improve issue quality and triage. It does not execute code, request secrets, or alter runtime behavior.

### Analysis Group #9

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard TypeScript compiler configuration file (tsconfig.json) for the CLI package of the SkillGuard monorepo. It contains only routine TypeScript compilation settings: targeting ES2020, using CommonJS modules, outputting to a 'dist' directory, enabling strict mode, esModuleInterop, and resolveJsonModule. There is nothing suspicious or security-relevant in this file.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> This is a standard TypeScript configuration file (`tsconfig.json`) for the CLI package. It contains typical compiler options such as targeting ES2020, using CommonJS modules, enabling strict mode, and outputting to a `dist` directory. There are no security implications, malicious configurations, or exploitable vectors present in this file.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is a standard TypeScript compiler configuration for the CLI package, intended to compile source code from `src` into CommonJS output in `dist` with modern ES2020 target and strict type-checking enabled. Its intent is build-time correctness and compatibility for a Node.js-style CLI, not runtime behavior manipulation or hidden execution.

### Analysis Group #10

- **Final Severity**: LOW
- **Confidence**: 72%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- A developer uses 'sg keygen --out secrets' to generate an Ed25519 keypair, then 'sg sign SKILL.md --key secrets/private.key' to cryptographically sign their skill file and produce a manifest.json, and finally 'sg verify SKILL.md manifest.json' to confirm integrity. This is standard PKI-style tooling applied to AI agent skills.
- This pattern is safe when used in a controlled local workflow where the operator intentionally chooses the output directory and trusts the network source. Correct usage should include: (1) writing keys only to a trusted directory owned by the current user, (2) setting strict permissions on private keys (e.g., `0o600`) and less strict on public keys, (3) preventing accidental overwrite (`flag: 'wx'` or explicit confirmation), (4) validating and normalizing output paths before writing, and (5) for downloaded files, verifying cryptographic integrity/signature (e.g., expected hash or signed manifest) before saving and executing. In short: local trusted path + least-privilege permissions + anti-overwrite + integrity verification.
- This is a security utility (SkillGuard) used to generate cryptographic keys, sign AI agent skills, and verify their integrity to prevent tampering and unauthorized code execution.

### Why this might be dangerous:
- N/A - The only minor concern is that the 'install' subcommand (visible at line 214) fetches remote files and writes them to a hooks directory, which could be a supply chain risk if the remote URLs are attacker-controlled. However, this is a standard package installation pattern and the URLs appear to be from the project's own repository. No hardcoded malicious URLs are visible in the truncated code.
- This becomes dangerous under specific conditions: (a) if the process runs with elevated privileges and `--out` (or other file targets) points to sensitive locations, writes can clobber critical files; (b) if output directories are attacker-controlled or world-writable, symlink attacks can redirect writes (e.g., `private.key` path linked to another file), causing data loss or privilege abuse; (c) default `writeFile` behavior truncates existing files, so users can silently destroy valid keys; (d) private key files may be created with overly permissive defaults, exposing signing authority; (e) remote hook installation (`fetch` + `writeFile`) without signature/hash validation enables supply-chain compromise if source, DNS, TLS trust, or upstream account is compromised. Missing safeguards include path boundary enforcement, symlink checks (`lstat`/`realpath`), secure file mode, overwrite protection, atomic writes, and cryptographic verification of downloaded artifacts.
- An attacker creates a seemingly harmless AI skill but includes a hidden recursive symlink (e.g., `ln -s . malicious_link`). They distribute this skill to a victim. When the victim installs it, the OpenClaw gateway's automatic startup hook attempts to verify the skill's integrity. The `scanDirectory` function follows the symlink infinitely, exhausting memory or call stack limits, and crashes the gateway. This creates a persistent DoS, as the gateway will crash every time it restarts until the malicious skill is manually deleted from the filesystem.

### Recommendations:
- [security_analyst] Accept as safe. The static analysis findings are false positives — filesystem writes are the documented and expected behavior of a keygen CLI tool. If a full audit is desired, review the complete 'install' command to confirm the remote URLs used for fetching hook files are pinned to trusted sources and consider adding integrity checks (e.g., subresource integrity hashes) for the fetched files.
- [intent_analyst] Harden all write paths: use `path.resolve` + allowlisted base directory checks; reject symlinks via `lstat` and verify parent `realpath`; create private keys with `mode: 0o600`; use `flag: 'wx'` (or prompt `--force`) to avoid silent overwrite; perform atomic write (temp file + rename). For remote installs, require signed manifests/checksums and verify before writing, restrict to HTTPS + host allowlist, and fail closed on verification mismatch. Also log destination paths clearly and add tests for overwrite, symlink, and permission edge cases.
- [adversarial_analyst] Replace `fs.stat()` with `fs.lstat()` in `scanDirectory` to properly detect symlinks. Either ignore symlinks entirely or implement a tracking mechanism (e.g., a `Set` of resolved absolute paths) to prevent infinite recursion. Additionally, ensure that any file writing operations involving fetched content strictly sanitize the destination filename to prevent path traversal attacks.

**security_analyst** (anthropic/claude-sonnet-4.6):
> The code is a CLI tool for cryptographic signing and verification of AI agent skill files (SKILL.md), which is exactly what the README documents. The filesystem write operations flagged by static analysis (writing private.key and public.key files) are the core, expected functionality of a 'keygen' command in a cryptographic key management tool. Writing keys to a user-specified output directory is entirely appropriate. The directory scanning, manifest generation, and file hashing operations are all consistent with the tool's stated purpose of establishing a chain of trust for agent skills. There is no obfuscation, no hardcoded exfiltration endpoints, no access to unrelated sensitive resources (like ~/.ssh), and no hidden payloads.

**intent_analyst** (openai/gpt-5.3-codex):
> This CLI is trying to perform legitimate security tooling tasks: generate keypairs, sign skills, verify manifests, and install hook files. The flagged `fs.writeFile` calls are expected for these features, but they sit in a gray area because they write sensitive material (`private.key`) and downloaded remote content to disk without strong file-permission, overwrite, path-safety, or integrity safeguards.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> While this code is a legitimate security tool designed to verify AI skill integrity, it contains a recursive directory scanning vulnerability. The `scanDirectory` function uses `fs.stat()` instead of `fs.lstat()`, meaning it automatically follows symlinks without tracking previously visited paths. An attacker can exploit this by crafting a skill directory containing a recursive symlink. When the OpenClaw gateway automatically verifies the skill on startup, it will trigger an infinite loop, leading to a stack overflow and a persistent Denial of Service (DoS). Furthermore, static findings indicate a file-fetching routine that could be vulnerable to arbitrary file overwrites if the destination filenames are not strictly sanitized.

#### Disagreements Between Models

- **security_analyst**: SAFE - The code is a CLI tool for cryptographic signing and verification of AI agent skill files (SKILL.md), which is exactly what the README documents. The filesystem write operations flagged by static anal
- **intent_analyst**: MEDIUM - This CLI is trying to perform legitimate security tooling tasks: generate keypairs, sign skills, verify manifests, and install hook files. The flagged `fs.writeFile` calls are expected for these featu
- **adversarial_analyst**: MEDIUM - While this code is a legitimate security tool designed to verify AI skill integrity, it contains a recursive directory scanning vulnerability. The `scanDirectory` function uses `fs.stat()` instead of 

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:27:52.597172+00:00*