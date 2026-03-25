# Security Audit Report: Starchild-ai-agent/official-skills/twelvedata

| Field | Value |
|-------|-------|
| Repository | [Starchild-ai-agent/official-skills/twelvedata](https://github.com/Starchild-ai-agent/official-skills/tree/main/twelvedata) |
| Commit | `76ee44dea5bb` |
| Commit Date | 2026-03-25T10:28:04Z |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T11:15:21.183637+00:00 |
| Overall Risk | **LOW** |
| Confidence | 86% |
| Files Scanned | 7 / 8 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 8 |
| Files analyzed | 7 |
| Skipped (unsupported type) | 1 |
| Skipped (too large) | 0 |
| Analyzed by language | skill_prompt: 1, python: 6 |
| Dominant language | Python (✅ supported) |
| Code coverage | 7/6 code files (117%) |
| Top file types | .py(6), .md(1), .png(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 1 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 2 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **https://api.twelvedata.com is the official and correct Twelve Data REST API base URL** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for api.twelvedata.com. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`api.twelvedata.com`

2. ❌ **Twelve Data is a legitimate, reputable financial data provider with accurate and reliable market data** *(confidence: 30%)*
   - 检验结果：Insufficient data for Twelve Data (twelvedata.com) — manual check recommended.
   - 关联主体：`Twelve Data (twelvedata.com)`

3. ❌ **The TWELVEDATA_API_KEY stored in the environment belongs to the legitimate operator and has not been compromised** *(confidence: 30%)*
   - 检验结果：Insufficient data for TWELVEDATA_API_KEY environment variable — manual check recommended.
   - 关联主体：`TWELVEDATA_API_KEY environment variable`

4. ❌ **User-supplied symbol strings are safely handled as query parameters by the Twelve Data API and cannot cause injection or unexpected behavior server-side** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for twelve data api query parameter handling. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`Twelve Data API query parameter handling`

5. ❌ **The core.http_client.get_aiohttp_proxy_kwargs() utility does not introduce untrusted proxy endpoints that could intercept API traffic** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for core.http_client proxy configuration. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`core.http_client proxy configuration`

6. ❌ **twelvedata is a reputable, widely-used service** *(confidence: 30%)*
   - 检验结果：Insufficient data for twelvedata — manual check recommended.
   - 关联主体：`twelvedata`

## Detailed Findings

### 🔵 Finding #1: Dangerous call: aiohttp.ClientSession()

- **File**: `tools/client.py`
- **Line**: 68
- **Severity**: LOW
- **Source**: static
- **Description**: Network request - potential data exfiltration vector [HARDCODED: all arguments are constants]

```
      63 |         headers = {
      64 |             "Accept": "application/json",
      65 |         }
      66 | 
      67 |         proxy_kw = get_aiohttp_proxy_kwargs(url)
>>>   68 |         async with aiohttp.ClientSession() as session:
      69 |             async with session.get(
      70 |                 url,
      71 |                 headers=headers,
      72 |                 params=params,
      73 |                 timeout=aiohttp.ClientTimeout(total=30),
```

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 72%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- The aiohttp.ClientSession() is used to make authenticated GET requests to the Twelve Data REST API (e.g., https://api.twelvedata.com/quote, /time_series, etc.) to retrieve stock prices, forex quotes, and historical OHLCV data as documented in SKILL.MD.
- Making asynchronous HTTP requests to the Twelve Data API to retrieve stock, forex market data, and time series information as advertised by the skill.
- The correct/safe usage pattern is to use `aiohttp.ClientSession` in an `async with` block (as done here) so connections are properly closed, set explicit timeouts (already present), and keep request targets constrained to trusted API domains. Safe usage also includes: (1) constructing URLs from constants or strict allowlists (e.g., only `api.twelvedata.com`), (2) validating user-provided symbols/intervals before putting them into `params`, (3) handling non-200 responses and JSON parse failures deterministically, (4) avoiding logging secrets such as API keys, and (5) applying rate limiting/retries with backoff for reliability. If proxy support is required, proxy config should be explicit and controlled by trusted deployment settings.

### Why this might be dangerous:
- N/A — the network call targets the documented Twelve Data API endpoint, not an attacker-controlled server. There is no evidence of hardcoded exfiltration URLs, obfuscated payloads, or access to unrelated system resources.
- If the `url` variable is constructed using unsanitized user input (e.g., allowing a user to specify the full URL rather than just the API endpoint/parameters), an attacker could potentially exploit this for Server-Side Request Forgery (SSRF). This would allow them to force the application to make requests to internal network resources or arbitrary external domains. However, standard API wrappers typically hardcode the base URL.
- This pattern becomes dangerous if the request destination or proxy can be influenced by untrusted input. Conditions that make it risky include: user-controlled `url` (enables SSRF to internal services), permissive proxy configuration (can route traffic to attacker infrastructure), blind forwarding of arbitrary query parameters/headers (can exfiltrate secrets or abuse upstream APIs), and missing outbound network restrictions. If API keys are included in query params and full URLs are logged, credentials may leak. If TLS verification is weakened or disabled elsewhere, responses can be intercepted/tampered with. So while this line is not malicious, the same primitive can be abused when surrounding controls are weak.

### Recommendations:
- [security_analyst] No action required. The static analysis finding is a false positive. Standard practice of reviewing that the base URL is indeed the Twelve Data API domain and that no secondary/hidden HTTP calls exist elsewhere in the codebase is sufficient due diligence.
- [adversarial_analyst] Ensure that the `url` variable is constructed securely, using a hardcoded base URL for the Twelve Data API. Validate and sanitize all user-supplied parameters (like symbols, intervals, or output sizes) against expected formats before including them in the request to prevent any potential injection or unexpected API behavior.
- [intent_analyst] Keep this as a normal feature but add/verify safeguards: enforce a strict host allowlist for outbound URLs, validate/sanitize all user-derived params, explicitly control proxy behavior (only trusted proxies), ensure TLS verification remains enabled, and avoid logging sensitive request data. Add structured error handling for HTTP status/timeout/JSON errors, and consider reusing a long-lived session for performance. If this client is ever exposed to user-supplied endpoints, add SSRF protections (DNS/IP blocklists for private ranges, scheme restrictions, and egress firewall rules).

**security_analyst** (anthropic/claude-sonnet-4.6):
> The flagged code is a standard aiohttp HTTP client call used to fetch data from the Twelve Data API. This is entirely expected behavior for a stock/forex data skill that must make network requests to an external financial data provider. The static analysis flag is a false positive — making HTTP GET requests to an API endpoint is the core documented functionality of this skill, not a data exfiltration vector. The URL is constructed from the Twelve Data API base URL, parameters are user-supplied query parameters (symbols, intervals, etc.), and the API key comes from an environment variable (TWELVEDATA_API_KEY), not a hardcoded credential.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code snippet shows a standard asynchronous HTTP GET request using `aiohttp.ClientSession()`. This is typical for an API client designed to fetch data from a remote service, such as the Twelve Data API. The code properly uses context managers, sets a reasonable timeout (30 seconds), and appears to handle proxy configurations. From an attacker's perspective, unless the `url` variable is entirely controlled by user input (which is highly unlikely for a specific API wrapper), there is no direct vulnerability or malicious behavior here.

**intent_analyst** (openai/gpt-5.3-codex):
> This code appears to be a normal API client pattern: it creates an `aiohttp.ClientSession` and performs a GET request to fetch Twelve Data market data with fixed headers, query params, and a 30-second timeout. The intent is legitimate integration with an external data provider, not covert behavior. The scanner flagged it because any outbound HTTP capability can be abused in other contexts, but in this snippet the call itself is a standard feature.

#### Disagreements Between Models

- **security_analyst**: SAFE - The flagged code is a standard aiohttp HTTP client call used to fetch data from the Twelve Data API. This is entirely expected behavior for a stock/forex data skill that must make network requests to 
- **adversarial_analyst**: SAFE - The provided code snippet shows a standard asynchronous HTTP GET request using `aiohttp.ClientSession()`. This is typical for an API client designed to fetch data from a remote service, such as the Tw
- **intent_analyst**: LOW - This code appears to be a normal API client pattern: it creates an `aiohttp.ClientSession` and performs a GET request to fetch Twelve Data market data with fixed headers, query params, and a 30-second

### Analysis Group #2

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a skill prompt/metadata file for a Twelve Data financial market data integration. The file defines a legitimate skill that provides stock and forex price data through documented API tools. The content is entirely consistent with its stated purpose: fetching market data (quotes, time series, EOD prices, batch queries, reference data) from the Twelve Data API. There are no prompt injection attempts, jailbreak backdoors, data exfiltration instructions, obfuscated payloads, or unexpected behaviors. The file is essentially documentation and configuration for a financial data skill.

**intent_analyst** (openai/gpt-5.3-codex):
> The provided content is a skill definition/prompt for integrating Twelve Data market APIs (stocks/forex quotes, prices, time series, and reference endpoints). There is no executable logic shown that performs data exfiltration, persistence, privilege escalation, obfuscation, or other suspicious behavior. The apparent intent is documentation and routing guidance for legitimate financial data retrieval.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided text is a standard Markdown documentation file (skill prompt) designed to instruct an LLM on how to use the Twelve Data API for stock and forex data. There are no hidden instructions, prompt injections, jailbreak attempts, or data exfiltration commands. It purely defines the available tools, their purposes, and provides syntax examples for the AI to follow.

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This is a read-only market data skill with no financial transaction capability, hardcoded API endpoints, environment-variable-sourced credentials, and user inputs limited to query parameters — the trust chain is shallow and well-contained, contingent only on Twelve Data being a legitimate service (ta_002) and the API key being transmitted securely (the query-parameter auth method should be reviewed against the header alternative noted in the code).*

This skill is a read-only market data retrieval tool that fetches stock and forex price data from the Twelve Data API. It performs no financial transactions, executes no trades, holds no funds, and constructs no calldata. The sensitive operations are limited to: (1) authenticating with a third-party API using a key stored in an environment variable, and (2) forwarding user-supplied symbol strings and query parameters to a hardcoded external API endpoint. The trust chain is simple and shallow — the API key never leaves the server-side environment in a user-visible way, the endpoint is hardcoded to api.twelvedata.com, and user inputs are limited to market symbols and date strings that are passed as query parameters (not executed). The primary risks are API key exposure through logs or error messages, and the integrity of the data returned by Twelve Data (which could mislead financial decisions if the service is compromised or returns bad data). There are no blockchain interactions, no wallet operations, and no calldata construction.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | Base URL for all Twelve Data API calls | `hardcoded` — tools/client.py — BASE_URL = 'https://api.twelvedata.com' (module-level constant) | ta_001, ta_002 | 🟢 LOW |
| Authorization | TWELVEDATA_API_KEY used to authenticate all API requests | `env_variable` — tools/client.py — self.api_key = api_key or os.environ.get('TWELVEDATA_API_KEY', ''); appended to every request as params['apikey'] = self.api_key | ta_003 | 🟡 MEDIUM |
| User Input | Symbol strings (e.g., 'AAPL', 'EUR/USD') passed to API as query parameters | `user_provided` — tools/quote.py:execute() — symbol parameter passed directly to client.get_quote(symbol=symbol); tools/time_series.py:execute() — symbol, interval, outputsize, start_date, end_date passed to client; tools/reference_data.py:execute() — query string passed to client.search_symbol(query=query) | ta_004 | 🟢 LOW |
| User Input | Batch symbol lists passed to twelvedata_quote_batch and twelvedata_price_batch | `user_provided` — tools/quote.py — TwelveDataQuoteBatchTool and TwelveDataPriceBatchTool accept lists of symbols (up to 120 per SKILL.md documentation) | ta_004 | 🟢 LOW |
| Calldata | No calldata — this skill performs no blockchain transactions | `hardcoded` — N/A — skill is read-only market data retrieval with no DeFi, wallet, or transaction functionality | — | 🟢 LOW |
| API Endpoints | Proxy configuration injected via get_aiohttp_proxy_kwargs(url) | `config_file` — tools/client.py — proxy_kw = get_aiohttp_proxy_kwargs(url) applied to every aiohttp session.get() call; implementation is in core.http_client (not visible in provided files) | ta_005 | 🟡 MEDIUM |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] The base URL 'https://api.twelvedata.com' matches the official Twelve Data API endpoint documented in their public documentation

**How to verify:** Visit https://twelvedata.com/docs and confirm the REST API base URL. Cross-reference with https://support.twelvedata.com. Check that the domain resolves to Twelve Data's infrastructure (WHOIS lookup on twelvedata.com).

**If wrong:** ⚠️ All market data would be fetched from an incorrect or compromised endpoint; financial analysis based on this data would be unreliable or manipulated

#### 2. [🔴 MUST] The TWELVEDATA_API_KEY is transmitted as a URL query parameter ('apikey=...') and this is an accepted authentication method per Twelve Data's official API documentation

**How to verify:** Check https://twelvedata.com/docs#authentication — confirm whether query parameter auth is supported and whether it poses additional logging risks compared to the Authorization header method. Review whether Twelve Data logs query parameters server-side.

**If wrong:** ⚠️ If query parameter auth is not officially supported, requests may fail. More importantly, if Twelve Data or any intermediate proxy logs full URLs, the API key would be exposed in plaintext logs — consider switching to the Authorization header method already noted in client.py comments

#### 3. [🟡 SHOULD] Twelve Data's API key scope is read-only for market data retrieval and does not grant account modification, billing changes, or data write permissions

**How to verify:** Review Twelve Data's API key management documentation at https://twelvedata.com/account/api-keys — check what permissions API keys grant and whether read-only scoping is available

**If wrong:** ⚠️ A compromised API key could allow an attacker to modify account settings, change billing information, or perform other account-level actions beyond just reading market data

#### 4. [🟡 SHOULD] The core.http_client.get_aiohttp_proxy_kwargs() function only reads proxy settings from standard environment variables (HTTP_PROXY, HTTPS_PROXY) and does not introduce hardcoded or externally-configurable proxy endpoints

**How to verify:** Inspect the source code of core/http_client.py in the host framework — verify the implementation of get_aiohttp_proxy_kwargs() and confirm it only uses standard proxy environment variables with no external configuration sources

**If wrong:** ⚠️ If proxy settings can be injected through untrusted configuration, all API traffic including the API key could be routed through an attacker-controlled proxy

#### 5. [🔵 NICE TO HAVE] Twelve Data has a documented security policy, uptime SLA, and data accuracy guarantees appropriate for the financial use cases this skill enables

**How to verify:** Review https://twelvedata.com/terms and https://twelvedata.com/pricing for SLA terms; check independent reviews of Twelve Data's data accuracy on financial data aggregator comparison sites

**If wrong:** ⚠️ Inaccurate or delayed market data could lead to incorrect financial analysis; agents making time-sensitive decisions based on stale or wrong prices could cause financial harm to users

---
*Generated by Skill Audit Agent V1 on 2026-03-25T11:15:21.183637+00:00*