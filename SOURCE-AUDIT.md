# GitHub Source & Privacy Audit

## Open Source Release Preparation

This repository has been comprehensively audited and sanitized for a public, open-source GitHub release. The application operates strictly offline with zero network connectivity and zero telemetry.

### Privacy Hardening & System Scrubbing

1. **Deterministic Path & Profile Redaction**:
   - Centralized `redactLocalPaths` in `src/core.cpp` transforms all user home directory paths (Windows `<drive>:\Users\<user>`, `<drive>:\Documents and Settings\<user>`, extended device UNC `\\?\<drive>:\Users\<user>`, Linux `/home/<user>`, and macOS `/Users/<user>`) into non-identifying `~` paths.
   - Profile-path masking operates purely via regular expressions without querying the current user identity (`GetUserName`, `USERPROFILE`, or `USERNAME`), preventing accidental identity leakage during the scrubbing process.
   - All tool exception failures (`src/tools.cpp`) pass through `redactLocalPaths`, ensuring that path escape errors or filesystem exceptions never leak developer or user directories into outputs or logs.
   - Chat title generation (`src/gui.cpp`) sanitizes initial user message drafts through `displaySafe` to prevent local paths from entering conversation headers.

2. **Prohibition of Host Profiling & System Inspection**:
   - `systemInfoCommand` in `src/tools.cpp` rejects common host inventory commands, including `whoami`, `hostname`, `systeminfo`, `wmic`, `msinfo32`, `dxdiag`, `ipconfig`, `ifconfig`, `getmac`, `arp`, `route`, `netstat`, `nbtstat`, `net user`, `quser`, `cmdkey`, `nltest`, `dsquery`, and `gpresult`.
   - PowerShell system identity and environment inspections (`[System.Environment]::UserName`, `[System.Environment]::MachineName`, `$env:USERNAME`, `$env:USERDOMAIN`, `$env:COMPUTERNAME`, `$env:USERPROFILE`, `$env:HOME`, `$env:HOMEPATH`) are explicitly blocked.
   - Python tool runtime (`runPython`) blocks identity and hardware queries, including `uuid.getnode()` (hardware MAC address), `socket.gethostname()`, `getpass.getuser()`, `pathlib.Path.home()`, `os.path.expanduser()`, `os.environ`, `os.environb`, `winreg`, and `platform.uname()`.
   - Python audit hook (`_fruitfly_install_offline_policy`) intercepts low-level events (`socket.*`, `subprocess.*`, `_winapi.CreateProcess`, `winreg.*`, `os.system`, etc.) before third-party code executes.

3. **Complete Telemetry & Analytics Opt-Out**:
   - Subprocess environments are scrubbed down to essential system paths (`PATH`, `PATHEXT`, `SYSTEMROOT`, `WINDIR`, `TEMP`, `TMP`, `COMSPEC`, `PYTHONIOENCODING`).
   - Standard telemetry opt-out flags are forcefully injected into every spawned subprocess environment:
     - `DO_NOT_TRACK=1`
     - `DOTNET_CLI_TELEMETRY_OPTOUT=1`
     - `DOTNET_SKIP_FIRST_TIME_EXPERIENCE=1`
     - `POWERSHELL_TELEMETRY_OPTOUT=1`
     - `HF_HUB_OFFLINE=1`
     - `HF_HUB_DISABLE_TELEMETRY=1`
     - `TRANSFORMERS_OFFLINE=1`
     - `PIP_NO_INDEX=1`
     - `NEXT_TELEMETRY_DISABLED=1`
     - `VCPKG_DISABLE_METRICS=1`
     - `HOMEBREW_NO_ANALYTICS=1`
     - `AZURE_CORE_COLLECT_TELEMETRY=0`
     - `CHECKPOINT_DISABLE=1`
     - `STNOUPGRADE=1`
     - `NPM_CONFIG_OFFLINE=true`
     - `NPM_CONFIG_AUDIT=false`
     - `NPM_CONFIG_UPDATE_NOTIFIER=false`
     - `GOTELEMETRY=off`

4. **Zero Networking & Package Download Blocking**:
   - Shell command router blocks URL schemes (`http://`, `https://`, `ftp://`, `sftp://`, `ssh://`, `wss://`), UNC shares (`\\`), package manager operations (`pip install`, `npm install`, `pnpm add`, `bun add`, `yarn add`, `cargo install`, `winget`, `choco`, `scoop`, `apt`, `brew`), and network clients (`curl`, `wget`, `nc`, `netcat`, `socat`).
   - CMake configuration forces network backends off in `llama.cpp` (`LLAMA_CURL=OFF`, `LLAMA_HTTPLIB=OFF`, `LLAMA_OPENSSL=OFF`, `GGML_RPC=OFF`).
   - Portable release scripts exclude `Qt6Network.dll` and Qt network bearer plugins.

5. **Release & Repository Hygiene**:
   - Dead / abandoned generator script `scripts/build_gui.py` has been purged.
   - Comprehensive `.gitignore` shields against accidental commitment of local chats, feedback memory, virtual environments, compiler debug files (`.pdb`, `.obj`), and IDE settings.
   - Release privacy scanner (`scripts/Test-ReleasePrivacy.ps1`) verifies the absence of configured developer usernames, user profiles, SSH/RSA private keys, GitHub PATs, and AWS access keys across all text and wide-character streams.
   - GitHub community templates (`.github/ISSUE_TEMPLATE`, `.github/PULL_REQUEST_TEMPLATE.md`) guide contributors while enforcing offline and privacy guidelines.
