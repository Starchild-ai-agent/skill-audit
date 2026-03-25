# Security Audit Report: Labmat2090/my-ministry-organizer

| Field | Value |
|-------|-------|
| Repository | [Labmat2090/my-ministry-organizer](https://github.com/Labmat2090/my-ministry-organizer) |
| Commit | `2e6aeae0defe` |
| Commit Date | 2026-03-02T13:21:25+01:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:30:19.186691+00:00 |
| Overall Risk | **MEDIUM** |
| Confidence | 89% |
| Files Scanned | 15 / 24 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 24 |
| Files analyzed | 15 |
| Skipped (unsupported type) | 9 |
| Skipped (too large) | 0 |
| Analyzed by language | javascript: 11, tool_config: 3, dependency: 1 |
| Dominant language | JavaScript (✅ supported) |
| Code coverage | 14/15 code files (93%) |
| Top file types | .js(11), .md(7), .json(4), (no ext)(1), .css(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 1 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 0 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 4 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **Google Fonts (fonts.googleapis.com / fonts.gstatic.com) is a legitimate Google service and has not been compromised at build time** *(confidence: 30%)*
   - 检验结果：Insufficient data for fonts.googleapis.com — manual check recommended.
   - 关联主体：`fonts.googleapis.com`

2. ❌ **The JSON import parser (used in Settings restore flow) validates input schema before writing to IndexedDB** *(confidence: 30%)*
   - 检验结果：Insufficient data for JSON import handler in src/app/settings or equivalent — manual check recommended.
   - 关联主体：`JSON import handler in src/app/settings or equivalent`

3. ❌ **Dexie 4.0.10 (npm package) is the legitimate, unmodified Dexie IndexedDB wrapper with no malicious code** *(confidence: 30%)*
   - 检验结果：Insufficient data for dexie@4.0.10 on npmjs.com — manual check recommended.
   - 关联主体：`dexie@4.0.10 on npmjs.com`

4. ❌ **next@14.2.18 is a legitimate, unpatched Next.js release with no known critical CVEs affecting static export mode** *(confidence: 30%)*
   - 检验结果：Insufficient data for next@14.2.18 on npmjs.com — manual check recommended.
   - 关联主体：`next@14.2.18 on npmjs.com`

## Detailed Findings

### 🔴 Finding #1: Dangerous pattern: Function() constructor

- **File**: `src\lib\utils.js`
- **Line**: 222
- **Severity**: CRITICAL
- **Source**: static
- **Description**: Dynamic code execution

```
     217 | /**
     218 |  * Debounce function
     219 |  */
     220 | export function debounce(func, wait) {
     221 |   let timeout;
>>>  222 |   return function executedFunction(...args) {
     223 |     const later = () => {
     224 |       clearTimeout(timeout);
     225 |       func(...args);
     226 |     };
     227 |     clearTimeout(timeout);
```

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard package-lock.json file for a Next.js/React application called 'my-ministry-organizer'. The dependencies are all well-known, legitimate packages: Next.js 14, React 18, Dexie (IndexedDB wrapper), date-fns, lucide-react (icons), uuid, and standard dev dependencies like TypeScript, ESLint, Tailwind CSS, and PostCSS. All resolved URLs point to the official npm registry (registry.npmjs.org), and the package names match their expected legitimate counterparts with no signs of typosquatting or dependency confusion. The integrity hashes are present for all packages. No suspicious or unexpected packages are included given the stated purpose of a local-first field service tracking app.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard, auto-generated `package-lock.json` file for a React/Next.js application. It contains standard dependencies (like React, Next.js, Dexie) and devDependencies (like ESLint, TailwindCSS, TypeScript) with no signs of malicious packages, typosquatting, or exploitable configurations. From an attacker's perspective, there is no attack surface here beyond standard supply chain risks inherent to all npm projects.

**intent_analyst** (openai/gpt-5.3-codex):
> This code is a standard `package-lock.json` for a Next.js/React offline organizer app, intended to pin exact dependency versions for reproducible builds. The listed libraries (`next`, `react`, `dexie`, `date-fns`, etc.) align with the app’s stated purpose (local data tracking, UI, IndexedDB storage) and there are no visible signs of malicious intent, hidden execution logic, or suspicious install-time behavior in the provided snippet.

### Analysis Group #2

- **Final Severity**: SAFE
- **Confidence**: 79%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This is the standard configuration file required for any TypeScript-based Next.js application to compile correctly and provide developer tooling support.
- This tsconfig.json is used by the TypeScript compiler and Next.js build tooling to define how TypeScript source files are compiled. The settings shown (strict mode, ESNext modules, bundler module resolution, Next.js plugin) are standard boilerplate for any Next.js + TypeScript project.
- The safe/correct usage pattern here is to treat this file as a compile-time guardrail, not runtime logic. In this configuration, strong settings like `strict`, `forceConsistentCasingInFileNames`, and `isolatedModules` are good for correctness, while `noEmit` is correct for Next.js projects where the framework handles builds. `paths` with `@/*` is a common maintainability improvement. The gray-area settings (`allowJs` and `skipLibCheck`) are acceptable when used intentionally: `allowJs` is useful during gradual migration from JavaScript to TypeScript, and `skipLibCheck` is commonly used to reduce type-checking overhead in dependencies. Safe use means pairing these with discipline: keep app code mostly in `.ts/.tsx`, progressively convert `.js` files, and run periodic stricter checks in CI (or temporarily disable these flags during audits) to catch hidden type issues.

### Why this might be dangerous:
- N/A. This is a static configuration file for the TypeScript compiler and cannot be directly exploited or abused.
- This becomes dangerous mainly through weakened type guarantees, not malicious behavior. Specific risky conditions: (1) `allowJs: true` permits untyped `.js` files to enter critical data paths, which can bypass strict typing assumptions and cause runtime bugs (e.g., malformed imported backup JSON handling or incorrect numeric calculations for hours). (2) `skipLibCheck: true` can conceal incompatible or incorrect type definitions in third-party packages, especially after dependency upgrades, leading to production runtime mismatch despite a clean type check. (3) In combination, teams may overestimate safety because `strict` is enabled, while actual coverage is partial. This is especially risky when handling user-imported files, date math, or storage serialization/deserialization, where subtle typing gaps can cause data corruption or crashes.

### Recommendations:
- [adversarial_analyst] No action required. The configuration is standard and secure.
- [security_analyst] No action required. This file is safe and represents standard Next.js TypeScript configuration.
- [intent_analyst] Keep this config, but add safeguards: 1) If migration is complete, set `allowJs` to `false`; otherwise restrict JS usage to specific folders and enforce `checkJs` for remaining JS files. 2) Add a CI job that periodically runs a stricter type pass (e.g., temporarily without `skipLibCheck`) to catch dependency type regressions early. 3) Add runtime validation for external/untrusted data (especially import JSON) using schema validation (e.g., Zod) since TypeScript alone cannot enforce runtime shape. 4) Pin dependency versions and review type-related changelogs during upgrades. 5) Document these tradeoffs in `CONTRIBUTING.md` so developers understand that `strict` is not absolute when `allowJs`/`skipLibCheck` are enabled.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard `tsconfig.json` file used to configure the TypeScript compiler for a Next.js project. It contains typical settings such as strict mode, module resolution, and path aliases. There are no executable components, malicious configurations, or vulnerabilities present in this file.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard Next.js TypeScript configuration file (tsconfig.json) with completely normal settings. It configures TypeScript compilation options appropriate for a Next.js application, including ES2020 target, strict mode, JSX preservation, and a path alias for '@/*'. There is nothing unusual, suspicious, or malicious about this configuration.

**intent_analyst** (openai/gpt-5.3-codex):
> This `tsconfig.json` is intended to configure a Next.js TypeScript project for a browser-based app with modern JavaScript targets, strict type checking, path aliases, and build-tool-friendly settings (`noEmit`, `moduleResolution: bundler`, Next plugin). The intent is clearly legitimate: improve developer experience and type safety while relying on Next.js/SWC for compilation rather than `tsc` output.

#### Disagreements Between Models

- **adversarial_analyst**: SAFE - The provided code is a standard `tsconfig.json` file used to configure the TypeScript compiler for a Next.js project. It contains typical settings such as strict mode, module resolution, and path alia
- **security_analyst**: SAFE - This is a standard Next.js TypeScript configuration file (tsconfig.json) with completely normal settings. It configures TypeScript compilation options appropriate for a Next.js application, including 
- **intent_analyst**: LOW - This `tsconfig.json` is intended to configure a Next.js TypeScript project for a browser-based app with modern JavaScript targets, strict type checking, path aliases, and build-tool-friendly settings 

### Analysis Group #3

- **Final Severity**: SAFE
- **Confidence**: 77%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This manifest.json is used by browsers to enable PWA installation features, allowing users to install the ministry organizer app on their home screen with the correct name, icons, theme color, and shortcuts. This is standard web development practice.
- This file is required for Progressive Web Apps to be installable on mobile devices and desktops, providing a native app-like experience by defining how the app should appear on the home screen and launch.
- The correct/safe pattern is to use manifest shortcuts only as navigation hints into app views (e.g., open the Add Entry screen), not as automatic state-changing commands. Safe handling means parsing `action` from the URL with a strict allowlist (e.g., only `add`, `progress`), treating unknown values as no-op, and requiring explicit user interaction before saving or modifying data. For privacy-first/offline apps, this preserves local-data integrity while still enabling convenient quick actions from the home screen. Keeping icon paths local and static, and using non-sensitive metadata in the manifest, is also the correct usage.

### Why this might be dangerous:
- None. This is a static configuration file parsed by the browser to handle UI presentation and shortcuts. It does not execute code or handle user input directly.
- This becomes dangerous if the app interprets `/?action=add` as an immediate command that writes data automatically on load. Under that condition, an external link, QR code, or social engineering message could trigger unintended local record creation or data manipulation without user confirmation. It is especially risky if multiple URL parameters can prefill values and are not validated (e.g., hours/counts out of range), or if the app executes privileged flows directly from URL input. In short: URL-driven deep links are safe for routing, but unsafe if they directly perform side effects.

### Recommendations:
- [security_analyst] No action required. This file is safe and follows standard PWA manifest conventions. The only minor note is that 'purpose': 'any maskable' should ideally be split into separate icon entries ('any' and 'maskable') per best practices, but this is a cosmetic/compatibility concern, not a security issue.
- [adversarial_analyst] No security action required. Ensure the referenced icon files exist in the public directory to prevent broken image links when users install the PWA.
- [intent_analyst] Keep manifest shortcuts, but enforce defensive routing rules: (1) allowlist accepted actions, (2) require explicit confirmation before any write operation, (3) validate and clamp all URL-derived values, (4) ignore unknown/malformed parameters, and (5) add tests to ensure deep links cannot auto-save or mutate data. Also consider adding shortcut icons for UX completeness, though that is not a security issue.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard Progressive Web App (PWA) manifest.json file for a ministry organizer application. It contains only expected PWA configuration fields including app name, display settings, theme colors, icon references, app shortcuts, and categories. There is nothing suspicious or unexpected in this file — it is a completely standard and benign web app manifest that aligns perfectly with the documented purpose of the application.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> This is a standard Web App Manifest (`manifest.json`) used to configure how a Progressive Web App (PWA) behaves when installed on a user's device. It contains basic metadata such as the app's name, icons, theme colors, and shortcuts. There is no executable code, and the configuration values are standard and benign. From an attacker's perspective, this file presents no attack surface and cannot be exploited.

**intent_analyst** (openai/gpt-5.3-codex):
> This `manifest.json` is a standard Progressive Web App configuration file intended to make the app installable and provide metadata (name, icons, colors, orientation, categories, and app shortcuts). Its intent appears legitimate and product-focused, not malicious. The only mild gray area is the shortcut URL pattern (`/?action=add`), which depends on how the app handles query parameters at runtime.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard Progressive Web App (PWA) manifest.json file for a ministry organizer application. It contains only expected PWA configuration fields including app name, display settings, theme col
- **adversarial_analyst**: SAFE - This is a standard Web App Manifest (`manifest.json`) used to configure how a Progressive Web App (PWA) behaves when installed on a user's device. It contains basic metadata such as the app's name, ic
- **intent_analyst**: LOW - This `manifest.json` is a standard Progressive Web App configuration file intended to make the app installable and provide metadata (name, icons, colors, orientation, categories, and app shortcuts). I

### Analysis Group #4

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**intent_analyst** (openai/gpt-5.3-codex):
> This code is a normal utility module for date formatting, calendar math, progress calculations, JSON import/export, and UI helpers; the flagged section is a standard debounce implementation that delays repeated function calls and does not use dynamic code execution. There is no `Function()` constructor, `eval`, or string-based execution in the provided snippet, so the static finding appears to be a false positive.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The static analysis tool has generated a false positive. It incorrectly flagged a standard JavaScript function expression (`return function executedFunction(...)`) as a dangerous `Function()` constructor (which allows dynamic code execution similar to `eval()`). The code provided is a completely standard, safe, and widely used implementation of a `debounce` utility function. There is no dynamic code execution occurring here.

**security_analyst** (anthropic/claude-sonnet-4.6):
> The static analysis tool produced a false positive by flagging a standard named function expression (`return function executedFunction(...args)`) as a `Function()` constructor call. This is a completely standard JavaScript debounce implementation with no dynamic code execution whatsoever. The `function executedFunction(...args)` syntax is simply a named function expression used for better stack traces — it is not the `Function()` constructor, which would look like `new Function(...)` or `Function(...)`. The entire utils.js file contains only benign utility functions: date formatting, calendar grid generation, progress calculation, JSON export/import via browser APIs, and standard helpers. There is no obfuscation, no external network calls, no access to sensitive system resources, and no data exfiltration.

## Scenario Simulation Results

### 🟢 Normal Usage
- **Risk Level**: NONE
- **Safe**: True
- **Description**: A Jehovah's Witness pioneer uses the app to log daily ministry hours, bible studies, and return visits. The utility functions handle date formatting, calendar grid generation, progress calculation, and local JSON export/import. All data remains on the user's device. Functions like formatDate, generateCalendarGrid, calculateProgress, and downloadJSON work as intended with no network calls or external dependencies. The debounce function safely delays input handling. Risk is minimal for intended use.

### 🔵 Adversarial Input
- **Risk Level**: LOW
- **Safe**: True
- **Description**: Most functions operate on primitive values (numbers, strings) with limited attack surface. validateHours() properly rejects non-numeric, negative, or >24 values. formatDate() and parseDate() operate on strings without eval or dynamic execution. The readJSONFile() function wraps JSON.parse in a try/catch, safely rejecting malformed JSON. The downloadJSON() function creates a Blob from serialized data — no code execution path exists. The cn() and debounce() functions have no injection vectors. No SQL, shell commands, or eval() are used anywhere. The main residual concern is that downloadJSON() uses document.createElement and appends to the DOM, but this is a standard browser pattern and does not execute arbitrary code.

### 🟢 Privilege Escalation
- **Risk Level**: NONE
- **Safe**: True
- **Description**: This is a client-side browser utility library with no server-side code, no authentication system, no file system access beyond the browser's sandboxed FileReader API, and no inter-user data model. There are no roles, tokens, or credentials to escalate. The FileReader API is sandboxed by the browser and can only read files explicitly selected by the user via a file input. No privilege escalation vectors exist in this codebase.

### 🟢 Data Exfiltration
- **Risk Level**: NONE
- **Safe**: True
- **Description**: The README explicitly states the app is offline-first with no telemetry, no cloud storage, and no network calls. The utility code confirms this — there are no fetch(), XMLHttpRequest, WebSocket, or image beacon patterns present. The downloadJSON() function exports data to a local file chosen by the user, which is the intended backup feature and fully user-controlled. The only theoretical concern is if a malicious actor modified the codebase to add network calls, but as written, no exfiltration is possible. The app's privacy-first design is reflected accurately in the code.

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill performs no external API calls, handles no credentials, executes no blockchain transactions, and stores all data exclusively in the user's own browser storage — the trust chain is entirely local and the skill's architecture is fully consistent with its documented privacy-first purpose, with the only meaningful trust dependencies being the integrity of npm packages (Dexie, Next.js) which can be verified via package-lock.json integrity hashes and npm audit.*

This skill is a fully client-side, offline-first Next.js static web application for tracking personal ministry hours. It has no backend API calls, no blockchain interactions, no authentication system, and no external data transmission. All data is stored locally via Dexie (IndexedDB wrapper) in the user's browser. The only external network requests are for Google Fonts (DM Sans, Playfair Display) loaded at build/render time, and npm package resolution at install time. The sensitive operations are extremely limited: reading/writing to localStorage (theme preference), reading/writing to IndexedDB (ministry data), and file-based JSON export/import. The trust chain is essentially: user → browser storage → user, with no third-party intermediaries handling personal data. The skill's documented purpose (private, offline-first personal tracker) is fully consistent with its actual architecture.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | Google Fonts API — DM Sans and Playfair Display loaded via next/font/google | `hardcoded` — src/app/layout.js — import { DM_Sans, Playfair_Display } from 'next/font/google' | ta_001 | 🟢 LOW |
| API Endpoints | No runtime API endpoints — app is configured as a fully static export | `hardcoded` — next.config.js:4 — output: 'export' disables all server-side API routes and runtime fetches | — | 🟢 LOW |
| User Input | Ministry time entries — hours, bible study counts, return visit counts, dates entered by user | `user_provided` — Inferred from README and Dexie dependency — user enters numeric values and dates via calendar UI, stored in IndexedDB | — | 🟢 LOW |
| User Input | JSON import — user uploads a previously exported JSON backup file for data restore | `user_provided` — Inferred from README: 'Go to Settings > Restore Data' — JSON file parsed and written to IndexedDB via Dexie | ta_002 | 🟡 MEDIUM |
| Authorization | Theme preference stored in localStorage | `user_provided` — src/app/layout.js — localStorage.getItem('theme') and localStorage.setItem('theme', ...) | — | 🟢 LOW |
| Authorization | No API keys, tokens, or credentials of any kind | `hardcoded` — No .env files, no API key references, no authentication libraries present in package.json or any config file | — | 🟢 LOW |
| User Input | npm dependency installation — package-lock.json defines exact dependency versions including Dexie, uuid, date-fns, lucide-react | `hardcoded` — package-lock.json — lockfileVersion 3, all dependencies pinned with integrity hashes | ta_003, ta_004 | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🟡 SHOULD] next@14.2.18 has no known critical or high CVEs affecting static export applications as of the current date

**How to verify:** Check https://github.com/advisories?query=next.js or run 'npm audit' in the project directory after installation; cross-reference with https://nextjs.org/blog/security

**If wrong:** ⚠️ A known vulnerability in Next.js 14.2.18 could introduce XSS or data leakage vectors even in static export mode, potentially exposing locally stored ministry data

#### 2. [🟡 SHOULD] dexie@4.0.10 package integrity hash in package-lock.json matches the published npm registry entry and contains no exfiltration code

**How to verify:** Run 'npm audit' and verify the integrity field for dexie in package-lock.json matches: check https://registry.npmjs.org/dexie/4.0.10 and compare the 'integrity' SHA-512 hash; optionally inspect node_modules/dexie/dist/ source after install

**If wrong:** ⚠️ A tampered Dexie package could silently POST all IndexedDB data to an external server, completely defeating the privacy-first design

#### 3. [🟡 SHOULD] The JSON import functionality in the settings page validates the structure of imported files before writing to IndexedDB

**How to verify:** Inspect src/app/settings/page.js (or equivalent) for the import handler — look for schema validation, try/catch around JSON.parse, and field-level type checking before db.bulkPut() or equivalent Dexie write calls

**If wrong:** ⚠️ A maliciously crafted JSON file could corrupt the user's local database or cause prototype pollution in the browser JavaScript context

#### 4. [🔵 NICE TO HAVE] The static export output contains no unexpected external script tags, tracking pixels, or fetch() calls to third-party domains

**How to verify:** Run 'npm run build', then inspect the generated 'out/' directory HTML and JS files for any src= or fetch() references to non-local domains; use browser DevTools Network tab on the running app and confirm zero external requests during normal use

**If wrong:** ⚠️ Any unexpected external call would contradict the privacy-first, no-telemetry claim and could be exfiltrating user ministry activity data

#### 5. [🔵 NICE TO HAVE] No Content Security Policy (CSP) header is needed for static file serving, but if deployed to a web server, a restrictive CSP should be configured

**How to verify:** Check deployment configuration (e.g., nginx, Vercel, Netlify) for CSP headers; a recommended CSP would be: default-src 'self'; script-src 'self'; connect-src 'none'

**If wrong:** ⚠️ Without a CSP, any XSS vulnerability (however unlikely in this app) would have no browser-level mitigation, potentially allowing injected scripts to read IndexedDB data

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:30:19.186691+00:00*