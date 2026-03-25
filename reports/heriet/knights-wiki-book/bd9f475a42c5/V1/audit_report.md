# Security Audit Report: heriet/knights-wiki-book

| Field | Value |
|-------|-------|
| Repository | [heriet/knights-wiki-book](https://github.com/heriet/knights-wiki-book) |
| Commit | `bd9f475a42c5` |
| Commit Date | 2023-08-13T20:57:48+09:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:37:03.382082+00:00 |
| Overall Risk | **LOW** |
| Confidence | 86% |
| Files Scanned | 5 / 133 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 133 |
| Files analyzed | 5 |
| Skipped (unsupported type) | 128 |
| Skipped (too large) | 0 |
| Analyzed by language | tool_config: 3, skill_prompt: 2 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 5/3 code files (100%) |
| Top file types | .md(118), .png(6), (no ext)(2), .yml(2), .xml(2), .json(1), .ico(1), .css(1) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 0 |
| 🟡 MEDIUM | 0 |
| 🔵 LOW | 1 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 4 |

## Trust Assumptions & Verification

### 🛡️ 安全运行的前提条件

以下为当前 skill 的核心信任假设。未通过验证的条件需要人工确认，否则相关风险不应降级。

1. ✅ **GitHub Actions official actions (actions/checkout@v3, actions/configure-pages@v3, actions/upload-pages-artifact@v1, actions/deploy-pages@v2) are maintained by GitHub and have not been compromised** *(confidence: 98%)*
   - 检验结果：github is in TRUSTED_SERVICES_WHITELIST; reputation verification is marked VERIFIED by policy.
   - 关联主体：`github.com/actions/*`
   - 证据：[Trusted service whitelist policy](https://github.com/)

2. ❌ **GitHub Pages deployment infrastructure (pages.github.com) correctly serves only the artifact uploaded by the workflow** *(confidence: 0%)*
   - 检验结果：Found 0 source(s) for pages.github.com. Confidence: 0%. Could not confidently confirm — manual check recommended.
   - 关联主体：`pages.github.com`

3. ❌ **The honkit/honkit Docker image on Docker Hub is the legitimate HonKit project image and has not been compromised or replaced with a malicious image** *(confidence: 30%)*
   - 检验结果：Insufficient data for hub.docker.com/r/honkit/honkit — manual check recommended.
   - 关联主体：`hub.docker.com/r/honkit/honkit`

4. ❌ **The :latest tag of honkit/honkit on Docker Hub resolves to the same trusted image across all CI runs (i.e., the tag has not been silently updated to a malicious version)** *(confidence: 30%)*
   - 检验结果：Insufficient data for honkit/honkit:latest — manual check recommended.
   - 关联主体：`honkit/honkit:latest`

## Detailed Findings

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a simple book.json configuration file for a GitBook or similar documentation tool. It contains only two fields: 'root' pointing to a local './docs' directory, and 'title' set to the name of a Nintendo DS game wiki. There is no executable code, no network requests, no system calls, no obfuscation, and no sensitive data access. It is entirely benign.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is a simple tool configuration object that sets the content root directory (`./docs`) and display title for a book/wiki build process. Its intent is straightforward: define where source documents live and what title to show in generated output. There is no executable logic, no dynamic input handling, and no indicators of obfuscation, exfiltration, or abuse behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided code is a standard, trivial JSON configuration file likely used by a static site generator (such as mdBook, GitBook, or HonKit) to define the root directory and title of a documentation project. From an attacker's perspective, this specific file contains no executable logic, malicious payloads, or prompt injection vectors. The only theoretical attack vector would involve an attacker modifying the 'root' value to perform a path traversal attack (e.g., setting it to '../../../etc'), hoping the underlying build tool blindly includes sensitive system files in the generated output. However, the code as written ('./docs') is completely benign and standard.

### Analysis Group #2

- **Final Severity**: SAFE
- **Confidence**: 77%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- Used to spin up a local HonKit development server that renders the wiki book content from the repository directory, allowing contributors to preview the book at http://localhost:4000 before publishing.
- This is a common and correct pattern for local docs development: run a docs tool in a container, expose its web preview, and mount source files from the host so changes are live. Safe usage means: (1) pinning the container image to a specific trusted version or digest instead of `latest`; (2) using the bind mount only for trusted local content you expect the container to read/write; (3) running this on a development machine/network where exposing `4000` is acceptable; and (4) ideally using least privilege defaults (non-root user, read-only filesystem where possible, and only required capabilities). In short, this pattern is safe for local dev when the image source is trusted and scope is controlled.
- This configuration is used to spin up a local development server to preview the Markdown documentation files as a rendered web-based book using HonKit.

### Why this might be dangerous:
- This becomes dangerous under a few conditions: if `honkit/honkit:latest` is compromised or changes unexpectedly, pulling `latest` can execute unreviewed code in your environment; because `./` is mounted into the container, any process in the container can modify repository files (or potentially sensitive files if the compose file is run from a broader directory); and the exposed port can make the service reachable from other hosts depending on Docker/network settings, which may leak draft/internal content. Risk increases if used in CI/CD or shared servers, if untrusted plugins/markdown are processed, or if the container runs as root with broad filesystem access. The dangerous combination is: mutable image tag + writable host bind mount + network exposure + insufficient runtime restrictions.
- There is no direct abuse scenario for this specific file in a local development context. However, if this Docker Compose file were mistakenly used to host the documentation on a public-facing production server, the HonKit development server (exposed on port 4000) might be subject to denial-of-service or other vulnerabilities inherent to development servers not hardened for production use.

### Recommendations:
- [security_analyst] No action required. The configuration is minimal and appropriate for its stated purpose. As a minor hardening note, pinning the image to a specific digest (e.g., honkit/honkit@sha256:...) rather than using 'latest' would improve reproducibility and supply-chain security, but this is a low-priority concern for a documentation project.
- [intent_analyst] Keep this setup for local development, but harden it: pin image version/digest (avoid `latest`), bind to localhost explicitly (`127.0.0.1:4000:4000`), make mount read-only if write access is unnecessary (`./:/work:ro`), run as non-root (`user:`), and consider `read_only: true`, `cap_drop: ["ALL"]`, and `security_opt: ["no-new-privileges:true"]` where compatible. Also keep project scope narrow (run compose from repo root only) and regularly review/lock dependencies/plugins used by HonKit.
- [adversarial_analyst] No changes are needed for local development. If the documentation is to be deployed to a production environment, it is recommended to build the static HTML files using HonKit and serve them via a production-ready web server (such as Nginx or Apache) rather than exposing the HonKit development server.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a straightforward Docker Compose configuration file for running a HonKit (static site/book generator) service. It uses the official honkit/honkit image, exposes port 4000 for local web preview, and mounts the current directory as a working volume. This is entirely consistent with the documented purpose of the repository — building and serving a wiki/book about the Nintendo DS game 'Knights in the Nightmare'. There is no obfuscation, no unexpected network access, no data exfiltration, and no suspicious behavior.

**intent_analyst** (openai/gpt-5.3-codex):
> This Docker Compose file is trying to provide a local documentation build/serve environment using the `honkit/honkit` container, mapping host port 4000 to container port 4000 and bind-mounting the repository into `/work` so edits on the host are immediately visible to HonKit. The intent appears legitimate (local wiki/book authoring), with no direct malicious behavior in the snippet itself.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided `compose.yml` file is a standard Docker Compose configuration used to run HonKit (a documentation building tool, forked from GitBook) for rendering the 'Knights in the Nightmare' wiki book. It maps the current directory to the container's working directory and exposes port 4000 for local viewing. There are no malicious configurations, excessive privileges, or suspicious commands present. It is a completely benign setup for local documentation development.

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a straightforward Docker Compose configuration file for running a HonKit (static site/book generator) service. It uses the official honkit/honkit image, exposes port 4000 for local web preview
- **intent_analyst**: LOW - This Docker Compose file is trying to provide a local documentation build/serve environment using the `honkit/honkit` container, mapping host port 4000 to container port 4000 and bind-mounting the rep
- **adversarial_analyst**: SAFE - The provided `compose.yml` file is a standard Docker Compose configuration used to run HonKit (a documentation building tool, forked from GitBook) for rendering the 'Knights in the Nightmare' wiki boo

### Analysis Group #3

- **Final Severity**: LOW
- **Confidence**: 76%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This workflow is used to automatically build and publish a Nintendo DS game wiki (Knights in the Nightmare) as a static website to GitHub Pages whenever changes are pushed to the main branch. This is a completely standard and expected use case for a documentation/wiki repository.
- Automating the build and deployment process of a static documentation site or wiki (specifically, a game wiki for 'Knights in the Nightmare') to GitHub Pages.
- This pattern is safe when used as a controlled publish pipeline for trusted content. Correct usage means: (1) only trusted maintainers can push to `main`; (2) build logic (`docker compose ... make build`) is deterministic and reviewed; (3) GitHub Actions used are pinned to immutable SHAs (or tightly managed versions) to reduce supply-chain risk; (4) least-privilege permissions are enforced per job (e.g., `contents: read` for build, `pages: write` and `id-token: write` only for deploy); and (5) branch protection + required reviews are enabled so workflow or Docker/compose changes cannot be merged unreviewed. In that setup, this is a standard and safe docs deployment workflow.

### Why this might be dangerous:
- To exploit this pipeline, an attacker would need to compromise a developer's account or bypass branch protections to commit directly to the `main` branch. Once write access is obtained, the attacker could modify the `docker-compose.yml`, `Makefile`, or HonKit plugins to execute arbitrary code during the `docker compose run --rm honkit make build` step, potentially exfiltrating repository contents or modifying the deployed GitHub Pages site to host malicious content.
- This becomes dangerous if an attacker can influence either workflow execution or dependencies. Specific conditions: (a) if someone can push directly to `main` (or merge unreviewed PRs), they can alter `docker-compose.yml`, Dockerfiles, Makefile, or workflow steps to run arbitrary code on GitHub-hosted runners and potentially exfiltrate secrets/tokens; (b) actions referenced by mutable major tags (`@v3`, `@v2`, `@v1`) could be abused if a third-party action release is compromised; (c) if default token permissions are broader than needed, a compromised build step could perform unintended repo/API actions; (d) if the build process fetches unpinned external dependencies, supply-chain poisoning could inject malicious behavior. So the pattern is not inherently malicious, but it is a privileged automation surface that must be hardened.

### Recommendations:
- [security_analyst] No action required. The workflow is safe and follows standard GitHub Actions patterns. Minor note: the actions are pinned to version tags (v1, v2, v3) rather than specific commit SHAs, which is a minor supply chain security consideration, but this is common practice and not a significant concern for a public documentation project.
- [adversarial_analyst] Maintain strict branch protection rules on the `main` branch, requiring pull request reviews and status checks before merging. Additionally, consider updating the GitHub Actions (e.g., `actions/checkout`, `actions/deploy-pages`) to their latest major versions to ensure you have the latest security patches and features.
- [intent_analyst] Harden this workflow with concrete controls: pin all actions to commit SHAs; add explicit `permissions` to each job (build: `contents: read`; deploy: only `pages: write` and `id-token: write` as already present); enforce branch protection on `main` (required PR reviews, status checks, no direct pushes); restrict who can modify workflow files and build scripts; pin Docker base images/dependencies and use lockfiles where possible; and optionally add artifact integrity checks/SBOM generation. These steps preserve the intended behavior while closing the main gray-area abuse paths.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a standard GitHub Actions CI/CD workflow file for building and deploying a static documentation site (HonKit/GitBook) to GitHub Pages. It checks out the repository, configures GitHub Pages, builds the documentation using Docker Compose, uploads the build artifact, and deploys it to GitHub Pages. All actions used are official GitHub-provided actions (actions/checkout, actions/configure-pages, actions/upload-pages-artifact, actions/deploy-pages), and the workflow follows standard best practices for GitHub Pages deployment.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> This is a standard, secure GitHub Actions workflow designed to build a static site using HonKit via Docker Compose and deploy it to GitHub Pages. From an attacker's perspective, there are no direct vulnerabilities within this YAML file. It does not process untrusted input, does not expose secrets, and only triggers on pushes to the `main` branch, which inherently protects it from malicious pull requests originating from forks. The execution relies entirely on the repository's internal `docker-compose.yml` and `Makefile`.

**intent_analyst** (openai/gpt-5.3-codex):
> This workflow is trying to do standard CI/CD for a documentation site: on pushes to `main`, it checks out the repo, configures GitHub Pages, builds a HonKit book via Docker Compose, uploads `_book/` as an artifact, and then deploys it to GitHub Pages. The intent is legitimate and typical for static-site publishing, with sensible job separation (`build` then `deploy`).

#### Disagreements Between Models

- **security_analyst**: SAFE - This is a standard GitHub Actions CI/CD workflow file for building and deploying a static documentation site (HonKit/GitBook) to GitHub Pages. It checks out the repository, configures GitHub Pages, bu
- **adversarial_analyst**: SAFE - This is a standard, secure GitHub Actions workflow designed to build a static site using HonKit via Docker Compose and deploy it to GitHub Pages. From an attacker's perspective, there are no direct vu
- **intent_analyst**: MEDIUM - This workflow is trying to do standard CI/CD for a documentation site: on pushes to `main`, it checks out the repo, configures GitHub Pages, builds a HonKit book via Docker Compose, uploads `_book/` a

### Analysis Group #4

- **Final Severity**: SAFE
- **Confidence**: 78%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This file serves as a game wiki reference document, providing players with detailed skill statistics for different character classes (Duelist, Warrior, Hermit, Archer, Priestess) in the game Knights in the Nightmare. It is part of a GitBook-style documentation project.
- This is exactly what the README claims it to be: a Wiki reference page converted into a Markdown book format, detailing the skill statistics and attributes for various character classes in a specific video game.
- The correct/safe use of this pattern is as read-only, curated content rendered in a Markdown viewer/site generator. Relative image paths (e.g., ./image/icon_*.png) are fine when the content repository is trusted and controlled. Safe handling includes: (1) treat this as data/documentation only, not scriptable input, (2) render with a Markdown engine configured to sanitize or disable raw HTML if any user edits are allowed, (3) keep links/images restricted to expected schemes and domains, and (4) validate formatting completeness (tables, rows, numeric fields) to prevent accidental data corruption in published guides.

### Why this might be dangerous:
- There is no direct exploitation scenario for this specific file. The only theoretical risk would be if the application parsing this Markdown has a severe vulnerability in its Markdown rendering engine (e.g., buffer overflow in table parsing), but that is a vulnerability in the parser, not a malicious design of this data.
- This becomes dangerous if the same Markdown pattern is accepted from untrusted contributors and rendered without sanitization. Specific risky conditions include: allowing raw HTML tags/events, permitting javascript: or data: URLs, or embedding remote images that leak reader IP/user-agent for tracking. In permissive renderers, attacker-supplied Markdown/HTML could trigger XSS, defacement, phishing links, or content spoofing. Another practical risk is integrity abuse: silent stat tampering in community-edited files (not code execution, but misinformation). In this snippet, the content is also truncated at the end, which can indicate incomplete parsing/export; if publishing pipelines do not validate completeness, readers may consume incorrect/incomplete data.

### Recommendations:
- [security_analyst] No action required. This file is benign game documentation content with no security concerns whatsoever.
- [adversarial_analyst] No security mitigation is necessary for this file. It is purely static, benign reference data.
- [intent_analyst] Keep this content in a trusted-docs pipeline and add guardrails: sanitize Markdown output, disable raw HTML where possible, whitelist allowed link/image protocols, enforce CSP on the hosting site, and require review/sign-off for content changes. Add CI checks for table integrity (row/column count, numeric field patterns, truncation detection) so accidental or malicious data edits are caught before publication.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a Markdown documentation page containing game skill data for the Nintendo DS game 'Knights in the Nightmare' (ナイツ・イン・ザ・ナイトメア). It consists entirely of Japanese-language tables describing character classes, skill names, damage values, hit counts, gem types, and injury rates. There is no executable code, no prompt injection, no obfuscation, no data exfiltration instructions, no hardcoded URLs, and no suspicious instructions of any kind. It is purely static reference/wiki content.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> As a red team researcher evaluating this for Prompt Injection, Jailbreaks, or Data Exfiltration, I found absolutely zero malicious indicators. This file consists entirely of static Markdown tables containing video game statistics (classes, elements, damage values, hit counts) for the Nintendo DS game 'Knights in the Nightmare'. There are no hidden LLM instructions, no prompt overrides, no external URLs (only local image paths), and no executable code. An attacker would not write a payload this way unless they were hiding instructions within the table cells, which is not occurring here.

**intent_analyst** (openai/gpt-5.3-codex):
> This file is trying to provide static reference data (a game wiki/book table of skills, damage, hit counts, and non-injury values) in Markdown format for "ナイツ・イン・ザ・ナイトメア." There is no executable logic, payload behavior, or direct sign of malicious intent; it is documentation content intended for display and lookup.

#### Disagreements Between Models

- **security_analyst**: SAFE - This file is a Markdown documentation page containing game skill data for the Nintendo DS game 'Knights in the Nightmare' (ナイツ・イン・ザ・ナイトメア). It consists entirely of Japanese-language tables describing 
- **adversarial_analyst**: SAFE - As a red team researcher evaluating this for Prompt Injection, Jailbreaks, or Data Exfiltration, I found absolutely zero malicious indicators. This file consists entirely of static Markdown tables con
- **intent_analyst**: LOW - This file is trying to provide static reference data (a game wiki/book table of skills, damage, hit counts, and non-injury values) in Markdown format for "ナイツ・イン・ザ・ナイトメア." There is no executable logic

### Analysis Group #5

- **Final Severity**: SAFE
- **Confidence**: 100%
- **Is Malicious**: False
- **Is Gray Area**: False
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a plain Markdown documentation page for a Nintendo DS game ('Knights in the Nightmare') wiki, describing game systems such as battle mechanics, tactics, unit stats, and setup screens. It contains no code, no scripts, no executable logic, no network requests, no data exfiltration, no obfuscation, and no prompt injection or jailbreak instructions. It is purely static game reference content written in Japanese.

**intent_analyst** (openai/gpt-5.3-codex):
> This is not executable code; it is a Japanese gameplay/system documentation page describing mechanics for a Nintendo DS strategy RPG (stats, attributes, battle loop, victory/defeat rules, item durability, unit VIT consumption, setup/intermission systems, and progression mechanics). The intent is informational/reference content for players, not automation, control flow, or system-level behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The provided text is a standard Markdown document containing wiki information about the game mechanics of the Nintendo DS game 'Knights in the Nightmare'. Despite being labeled as 'skill_prompt', it contains no AI instructions, prompt templates, executable code, or hidden payloads. There are no prompt injection vulnerabilities, jailbreak backdoors, or data exfiltration instructions present.

## Trust Chain Analysis

**Overall Trust Verdict**: ✅ **TRUSTED**
*This skill is a fully static game wiki with no API calls, no credentials, no user inputs, and no financial operations — its only trust dependencies are the standard GitHub Actions and Docker Hub supply chain, which are low-risk for a read-only static site deployment.*

This skill is a static Nintendo DS game wiki (Knights in the Nightmare) packaged as a HonKit book, deployed to GitHub Pages via a CI/CD pipeline. It performs no API calls, handles no credentials, executes no financial transactions, and accepts no user-controlled runtime inputs. The only 'sensitive operations' present are: (1) a GitHub Actions CI/CD pipeline that builds a Docker container and deploys to GitHub Pages, and (2) Docker image pulling from Docker Hub. All content is hardcoded static markdown. There are no API keys, wallet addresses, calldata, or dynamic data flows of any kind. The trust surface is essentially limited to the integrity of the GitHub Actions ecosystem, the honkit/honkit Docker image, and the repository itself.

*(Trust assumptions and verification details are shown at the top of this report.)*

### Data Flow Sources

| Category | Description | Source | Required Trust | Risk |
|----------|-------------|--------|----------------|------|
| API Endpoints | GitHub Actions workflow calls GitHub's own infrastructure endpoints for checkout, pages configuration, artifact upload, and deployment | `hardcoded` — .github/workflows/ci.yml — uses actions/checkout@v3, actions/configure-pages@v3, actions/upload-pages-artifact@v1, actions/deploy-pages@v2 (all official GitHub Actions) | ta_001, ta_002 | 🟢 LOW |
| API Endpoints | Docker image pull from Docker Hub for honkit/honkit:latest during CI build | `hardcoded` — compose.yml — image: honkit/honkit:latest | ta_003, ta_004 | 🟡 MEDIUM |
| Authorization | GitHub Actions OIDC id-token used for GitHub Pages deployment | `env_variable` — .github/workflows/ci.yml — permissions: id-token: write, used by actions/deploy-pages@v2 | ta_001 | 🟢 LOW |
| User Input | Static markdown content authored in the repository (game wiki data) | `hardcoded` — docs/Skill.md, docs/System.md, book.json — all static, committed content with no runtime user input | — | 🟢 LOW |
| Calldata | No calldata, transactions, or blockchain interactions of any kind | `hardcoded` — N/A — no blockchain, DeFi, or transaction-related code exists anywhere in this skill | — | 🟢 LOW |
| Contract Addresses | No contract or wallet addresses present | `hardcoded` — N/A — no blockchain-related content exists in this skill | — | 🟢 LOW |

### Manual Verification Checklist

> Items that cannot be auto-verified — check these before deploying this skill.

#### 1. [🟡 SHOULD] The honkit/honkit Docker Hub image should be the official image maintained by the HonKit project (https://github.com/honkit/honkit), not a typosquat or abandoned image

**How to verify:** 1. Visit https://hub.docker.com/r/honkit/honkit and check the linked source repository. 2. Confirm the source repo matches https://github.com/honkit/honkit. 3. Check that the image has recent pushes and a reasonable download count consistent with a legitimate project.

**If wrong:** ⚠️ A malicious or abandoned Docker image could execute arbitrary code during CI builds, potentially injecting malicious JavaScript into the deployed static wiki pages that would run in visitors' browsers.

#### 2. [🟡 SHOULD] The GitHub Actions used (actions/checkout@v3, actions/configure-pages@v3, actions/upload-pages-artifact@v1, actions/deploy-pages@v2) should be pinned to their expected commit SHAs to prevent mutable tag attacks

**How to verify:** 1. Visit each action's GitHub repository (e.g., https://github.com/actions/checkout). 2. Check that the v3/v1/v2 tags point to the expected commits. 3. Best practice: replace version tags with full commit SHAs in the workflow file.

**If wrong:** ⚠️ If a version tag is moved to point to a different (malicious) commit, the CI pipeline would execute attacker-controlled code with access to repository contents and the deployment token.

#### 3. [🔵 NICE TO HAVE] The honkit/honkit:latest Docker image should be pinned to a specific digest (e.g., honkit/honkit@sha256:...) rather than the mutable :latest tag

**How to verify:** 1. Run: docker pull honkit/honkit:latest && docker inspect honkit/honkit:latest --format='{{index .RepoDigests 0}}' to get the current digest. 2. Pin compose.yml to use that digest instead of :latest.

**If wrong:** ⚠️ Without a digest pin, a future update to the :latest tag on Docker Hub (whether malicious or accidental) would automatically be used in all subsequent CI builds without any repository change or review.

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:37:03.382082+00:00*