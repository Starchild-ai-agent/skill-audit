# Security Audit Report: quasi/cl-memcached

| Field | Value |
|-------|-------|
| Repository | [quasi/cl-memcached](https://github.com/quasi/cl-memcached) |
| Commit | `e0f718770174` |
| Commit Date | 2026-01-21T16:41:33+05:30 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:36:20.582584+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 88% |
| Files Scanned | 2 / 43 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 43 |
| Files analyzed | 2 |
| Skipped (unsupported type) | 41 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 1, tool_config: 1 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 2/1 code files (100%) |
| Top file types | .md(36), .lisp(3), .asd(2), (no ext)(1), .yaml(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 0 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 2 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **The IP address and port passed to make-memcache resolve to a trusted, application-controlled memcached server** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for memcached server ip:port (default 127.0.0.1:11211). Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`memcached server IP:port (default 127.0.0.1:11211)`

2. ❌ **The memcached server is deployed on a network segment inaccessible to untrusted parties (e.g., localhost or private LAN with firewall)** *(confidence: 30%)*
   - 检验结果：Insufficient data for memcached server network exposure — manual check recommended.
   - 关联主体：`memcached server network exposure`

3. ❌ **Cache keys used with this library are validated by the calling application to contain no whitespace, newlines, or control characters before being passed to cl-memcached functions** *(confidence: 30%)*
   - 检验结果：Insufficient data for memcached key sanitization — manual check recommended.
   - 关联主体：`memcached key sanitization`

4. ❌ **The Babel encoding library correctly length-prefixes value data in the TEXT protocol, preventing value content from being interpreted as protocol commands** *(confidence: 30%)*
   - 检验结果：Insufficient data for Babel library (babel on Quicklisp/ASDF) — manual check recommended.
   - 关联主体：`Babel library (babel on Quicklisp/ASDF)`

5. ❌ **The application does not cache security-sensitive data (passwords, private keys, session secrets) in memcached without additional encryption, given memcached's lack of at-rest or in-transit encryption** *(confidence: 30%)*
   - 检验结果：Insufficient data for memcached data confidentiality model — manual check recommended.
   - 关联主体：`memcached data confidentiality model`

## Detailed Findings

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 77%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file serves as a reference guide for AI agents or developers implementing memcached caching in Common Lisp applications. It documents the cl-memcached library's API, architecture, protocols (TEXT and META), and provides exemplar patterns like cache-aside. It is a standard developer/agent skill template.
- The correct/safe use of this pattern is as an application-side caching layer with explicit failure tolerance: (1) treat cache as non-authoritative unless your design explicitly allows otherwise, (2) use bounded TTLs for all cached entries, (3) use CAS (`mc-gets` + `mc-cas`) for concurrent updates, (4) validate and normalize keys/values before storage, (5) isolate dangerous admin-like commands (`mc-flush-all`) behind privileged operational controls, and (6) wrap all cache I/O with error handling (e.g., fallback to source of truth on `memcached-server-unreachable`). Connection pooling (`*mc-use-pool*`) is safe when pool size and timeouts are tuned for workload and thread model. For sensitive data, store only non-sensitive or encrypted/tokenized payloads and keep memcached on trusted internal networks.
- This is a legitimate skill prompt used to provide context, architectural understanding, and code examples to an AI agent so it can correctly generate Common Lisp code using the `cl-memcached` library.

### Why this might be dangerous:
- This becomes dangerous under specific conditions: if an attacker can reach memcached directly (open network exposure, no segmentation), they can read/overwrite/delete cache entries or trigger mass invalidation via flush operations; if the app blindly trusts cached objects, cache poisoning can alter business logic or authentication state; if shared global bindings (`*memcache*`) are used carelessly across tenants/environments, cross-context data leakage can occur; if unbounded key/value inputs are accepted, memory pressure and eviction storms can become denial-of-service vectors; if `noreply` is overused, write failures can go undetected and corrupt expected state transitions. Missing safeguards that should be present include: strict network ACLs/firewalling, environment/tenant namespacing for keys, input size limits and key policy enforcement, explicit timeout/retry/circuit-breaker settings, audit controls for destructive commands, and clear guidance against caching secrets in plaintext.
- The skill prompt itself cannot be directly abused as it contains no malicious instructions. However, from an attacker's perspective, if the underlying `cl-memcached` library does not properly sanitize inputs (such as spaces or newline characters in cache keys), an attacker could potentially perform Memcached protocol injection (CRLF injection) in the TEXT protocol. This would be an exploit against the application using the library, not the AI skill itself.

### Recommendations:
- [security_analyst] No action required. This file is safe and serves its documented purpose as a skill template for cl-memcached usage in Common Lisp applications.
- [intent_analyst] Keep this skill content, but harden guidance with a security/operations section: mandate private-network-only memcached access, require key namespacing and max key/value sizes, recommend default TTL caps, document when CAS is required, discourage `noreply` for critical writes, and restrict `mc-flush-all` to administrative workflows. Add concrete production checklists (timeouts, retries, circuit breakers, pool sizing, observability/metrics) and examples that show safe fallback behavior when cache is unavailable.
- [adversarial_analyst] The skill prompt is safe and requires no changes. As a general security practice for the underlying library, ensure that `cl-memcached` properly validates and sanitizes cache keys and values to prevent protocol injection attacks, especially when using the TEXT protocol.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This SKILL.md file is a legitimate skill template for LLM agents working with the cl-memcached Common Lisp library. It contains documentation, architecture diagrams, operation references, and code examples that are entirely consistent with its stated purpose of helping agents implement memcached caching. There are no prompt injection attempts, jailbreak backdoors, data exfiltration instructions, or any other malicious content. The code examples shown are standard memcached operations (get, set, delete, counters, etc.) with no hidden payloads or obfuscated instructions.

**intent_analyst** (openai/gpt-5.3-codex):
> This content is a skill/template and usage documentation for the `cl-memcached` Common Lisp library, intended to help developers implement cache operations (set/get/CAS/counters/pooling, TEXT and META protocols) correctly and efficiently. The intent is clearly legitimate: improve application performance and reliability via memcached patterns, with examples for cache-aside behavior and connection handling.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided `SKILL.md` is a standard, benign documentation template designed to teach an LLM how to use the `cl-memcached` Common Lisp library. It contains no prompt injection, jailbreak backdoors, or data exfiltration instructions. It accurately describes the library's API, architecture, and usage patterns without introducing any malicious directives to the AI agent.

#### Disagreements Between Models

- **security_analyst**: SAFE - This SKILL.md file is a legitimate skill template for LLM agents working with the cl-memcached Common Lisp library. It contains documentation, architecture diagrams, operation references, and code exa
- **intent_analyst**: LOW - This content is a skill/template and usage documentation for the `cl-memcached` Common Lisp library, intended to help developers implement cache operations (set/get/CAS/counters/pooling, TEXT and META
- **adversarial_analyst**: SAFE - The provided `SKILL.md` is a standard, benign documentation template designed to teach an LLM how to use the `cl-memcached` Common Lisp library. It contains no prompt injection, jailbreak backdoors, o

### Analysis Group #2

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file serves as a structured knowledge base or 'canon' document for AI agents and developers working with the cl-memcached library. It documents the library's features, confidence levels, test coverage, design decisions, and quality metrics to help agents understand the codebase without reading all source files directly.
- This file is used to provide structured context about a software project to an AI agent, detailing its architecture, dependencies, test coverage, and historical decisions to help the AI understand the codebase.
- The correct/safe usage pattern is to use this library strictly as an internal cache layer: create memcache instances with controlled host/port, keep memcached bound to private network interfaces, use connection pooling for performance, and store only non-sensitive or encrypted data with explicit TTLs. Prefer deterministic key schemes (namespaced keys), validate key/value sizes before writes, and use CAS/gets for race-sensitive updates. Treat cached data as untrusted when reading back (especially if values originate from users), use safe serialization formats, and enforce fallback behavior on cache miss or server failure. Monitor with stats commands and set operational limits (max item size, eviction policy awareness, timeout handling).

### Why this might be dangerous:
- As a static YAML configuration file with no active instructions or executable code, there is no direct abuse scenario within the file itself. An attacker could theoretically modify the repository URL or dependency links to point to a malicious fork, but the provided text contains the legitimate repository.
- This becomes dangerous when memcached is reachable from untrusted networks, when applications trust cache contents as authoritative, or when unsafe object deserialization is used. Specific risky conditions: (1) exposed memcached port (11211) with no firewall/auth allows remote cache poisoning, data scraping, and denial-of-service via memory pressure; (2) unvalidated user-controlled keys can enable namespace collisions, key flooding, or targeted overwrites; (3) storing sensitive session/auth data without encryption or integrity checks can lead to account takeover if cache is read/modified; (4) relying on cache writes with noreply and no verification can hide failures and create consistency bugs; (5) missing CAS in concurrent update paths can cause lost updates and stale state reuse.

### Recommendations:
- [security_analyst] No action required. This file is safe and serves a legitimate documentation/metadata purpose for the cl-memcached library.
- [adversarial_analyst] No action required. The file is safe. As a general best practice, ensure that the parser reading this YAML file uses a safe loader to prevent YAML deserialization attacks (e.g., avoiding PyYAML's unsafe loader).
- [intent_analyst] Keep severity SAFE for the provided artifact, but add deployment-level safeguards: restrict memcached to localhost/private VLAN, enforce firewall rules, disable public exposure, and use TLS/stunnel or trusted network segments. In application code, validate keys and value sizes, namespace keys, set TTLs explicitly, avoid unsafe deserialization, and never treat cache as sole source of truth for authorization-critical decisions. For concurrent mutations, require CAS where correctness matters. Add explicit error handling/observability around cache failures, and include security-focused tests (cache poisoning, oversized keys, stale CAS, unavailable cache node) to prevent gray-area misuse from becoming a production vulnerability.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a canon.yaml configuration/metadata file for the cl-memcached Common Lisp library. It contains project metadata, source analysis results, feature documentation, dependency listings, quality metrics, and architectural notes generated by a triangulation tool. There is nothing malicious, suspicious, or security-relevant in this file. It is purely descriptive metadata about the cl-memcached library's structure, features, and implementation decisions. All dependencies listed (usocket, split-sequence, babel, pooler, fiveam) are well-known, legitimate Common Lisp libraries appropriate for a memcached client implementation.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided `canon.yaml` file is a standard metadata and configuration file describing the structure, features, and metrics of the `cl-memcached` Common Lisp library. It contains no executable code, prompt injections, jailbreaks, or malicious instructions. It simply serves as structured context or documentation for an AI tool or agent.

**intent_analyst** (openai/gpt-5.3-codex):
> The provided content appears to be project metadata/specification for a Common Lisp memcached client (cl-memcached), not malicious logic. Its intent is legitimate: define architecture, supported features (TEXT and META protocols), confidence/coverage metrics, and operational guidance for connection pooling and cache commands. The only gray-area aspect is not malicious code, but operational risk: memcached integrations are safe in normal use yet can become dangerous if deployed without network, input, and serialization safeguards.

## Trust Chain Analysis

**Overall Trust Verdict**: ⚠️ **VERIFY_BEFORE_USE**
*The skill itself is a straightforward, locally-scoped caching library with no external API dependencies or credentials, but safe use requires verifying that the target memcached server is network-isolated (ta_002), that cache keys are sanitized against protocol injection (ta_003), and that sensitive data is not stored in plaintext without additional protection (ta_005) — none of which the library enforces itself.*

cl-memcached is a Common Lisp client library for the memcached caching protocol. It has no API endpoints, no blockchain components, no API keys, and no external service calls beyond the memcached server connection itself. The sensitive operations are limited to: (1) network connections to a memcached server at a user-specified IP/port, (2) serialization and transmission of arbitrary key-value data over that connection, and (3) construction of protocol commands (TEXT and META) from user-supplied keys and values. The trust chain is simple and local: the skill's safety depends almost entirely on whether the memcached server being connected to is trusted, and whether the keys and values passed to it are appropriately sanitized by the calling application. There are no third-party API dependencies, no credential flows, and no blockchain interactions. The primary risks are injection via malformed keys/values into the memcached protocol wire format, and data confidentiality/integrity risks if the memcached server is not on a trusted network.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | Memcached server IP address and port used to establish socket connections | `user_provided` — README.md and make-memcache function: (make-memcache :ip '127.0.0.1' :port 11211) — defaults to localhost:11211 but caller can supply any IP/port | ta_001, ta_002 | 🟡 MEDIUM |
| User Input | Cache keys passed to mc-set, mc-get, mc-delete, mc-incr, mc-decr, and all other operations | `user_provided` — SKILL.md and README.md: all public API functions accept a 'key' parameter directly from the caller — e.g. (mc-set key data ...) with no documented sanitization | ta_003 | 🟡 MEDIUM |
| User Input | Cache values (data) passed to storage operations (mc-set, mc-add, mc-replace, mc-append, mc-prepend, mc-cas) | `user_provided` — README.md: mc-set accepts arbitrary 'data' parameter; SKILL.md confirms storage of arbitrary values including session data and application objects | ta_004 | 🟢 LOW |
| User Input | CAS unique tokens used in mc-cas operations for optimistic locking | `user_provided` — README.md: mc-cas key data cas-unique — the cas-unique value is obtained from a prior mc-gets call and passed back by the caller | ta_001, ta_002 | 🟢 LOW |
| Authorization | Memcached authentication credentials (if any) | `user_provided` — README.md and SKILL.md: No authentication parameters are documented in make-memcache or any API function. Standard memcached has no built-in auth in TEXT protocol; SASL auth is not mentioned. | ta_002, ta_005 | 🟡 MEDIUM |
| User Input | Connection pool size and timeout parameters | `user_provided` — README.md: make-memcache :pool-size parameter (default 2); mc-set :timeout parameter (default 0 = no expiry) | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The memcached server targeted by make-memcache is bound to localhost or a private network interface, not 0.0.0.0 or a public IP

**How to verify:** On the server running memcached, run: 'ss -tlnp | grep 11211' or 'netstat -tlnp | grep 11211'. The listening address should be 127.0.0.1 or a private RFC1918 address, not 0.0.0.0 or a public IP. Also check memcached startup flags for '-l 127.0.0.1'.

**If wrong:** ⚠️ If memcached is publicly exposed, any internet host can read, write, or delete all cached data without authentication. This has been exploited in mass data breaches and DDoS amplification attacks historically.

#### 2. [🔴 MUST] The cl-memcached library's key-handling code validates or rejects keys containing spaces, newlines (\r, \n), or null bytes before constructing TEXT protocol commands

**How to verify:** Inspect cl-memcached.lisp (711 lines per canon.yaml). Search for key validation logic before command construction. Test empirically: attempt (mc-set "foo\r\nset injected 0 0 5\r\nhello" "value") and observe whether the library rejects it or sends two commands to the server.

**If wrong:** ⚠️ If keys are not sanitized, an agent or user who controls key values (e.g., derived from user input or external API responses) could inject arbitrary memcached commands, enabling cache poisoning or data exfiltration.

#### 3. [🔴 MUST] The usocket dependency used for socket I/O is sourced from the official Quicklisp distribution and has not been tampered with

**How to verify:** Check the installed usocket version against the official Quicklisp dist: compare (asdf:system-version (asdf:find-system :usocket)) against https://quicklisp.org/beta/releases.html. Verify the SHA256 of the downloaded archive matches the Quicklisp dist metadata.

**If wrong:** ⚠️ A tampered usocket could intercept all socket communications, redirect connections to attacker-controlled servers, or exfiltrate cached data.

#### 4. [🟡 SHOULD] The connection pool implementation (pooler library) correctly handles thread safety and does not allow connection objects to be shared simultaneously across threads

**How to verify:** Review pooler library source for mutex/lock usage around connection acquisition and release. The README claims thread-safety; verify by inspecting pooler's acquire/release logic for proper locking primitives on SBCL (sb-thread:with-mutex) and CCL (ccl:with-lock-grabbed).

**If wrong:** ⚠️ If connections are shared across threads without proper locking, interleaved writes from multiple threads could corrupt the protocol stream, causing one thread's data to be written under another thread's key, leading to data corruption or information leakage between concurrent requests.

#### 5. [🟡 SHOULD] The default timeout=0 behavior (items never expire) is intentional and the calling application sets appropriate TTLs for sensitive cached data

**How to verify:** Review application code using this skill for mc-set calls. Confirm that session tokens, authentication data, or time-sensitive values use explicit :timeout values rather than the default 0. Check memcached documentation confirming timeout=0 means no expiration.

**If wrong:** ⚠️ Sensitive cached data (e.g., session tokens, temporary access grants) that should expire will persist indefinitely, extending the window of exposure if the memcached server is later compromised or accessed by an unauthorized party.

#### 6. [🔵 NICE TO HAVE] The Babel library version in use correctly handles UTF-8 encoding edge cases (overlong encodings, surrogate pairs) without producing output that could affect protocol framing

**How to verify:** Check installed Babel version via (asdf:system-version (asdf:find-system :babel)) against https://common-lisp.net/project/babel/. Review Babel changelog for any encoding correctness fixes. Test with multi-byte Unicode values to confirm byte counts match declared lengths in protocol commands.

**If wrong:** ⚠️ If Babel produces incorrect byte counts for multi-byte characters, the declared data length in the memcached command would not match the actual bytes sent, potentially causing protocol desynchronization or data truncation.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:36:20.582584+00:00*