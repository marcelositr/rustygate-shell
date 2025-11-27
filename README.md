# RustyGate --- Restricted Security Shell (Alpha Stage)

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Status](https://img.shields.io/badge/release-ALPHA-orange)
![Build](https://img.shields.io/badge/Rust-stable-blue?logo=rust)
![POSIX](https://img.shields.io/badge/POSIX-Compliant-green)
![Security](https://img.shields.io/badge/Focus-Security%20Hardening-red)
![AIAssisted](https://img.shields.io/badge/AI_Assisted-Yes-purple?logo=openai)

------------------------------------------------------------------------

## 1. Overview

**RustyGate** is a high‑security, restricted‑execution shell designed to replace traditional user shells in controlled or sensitive
environments.\
It enforces strict command whitelisting, kernel‑level execution, immutable configuration rules, and comprehensive non‑repudiation auditing.

Unlike restrictive shells such as `rbash`, or pseudo-sandboxes built on top of Bash, **RustyGate is a native REPL written in Rust**, engineered for predictability, safety, and hardening at all layers.

The project is currently in **ALPHA**, focused on establishing a robust security‑first foundation.

------------------------------------------------------------------------

## 2. Core Objectives

### 2.1 Primary Technical Goal

Create a **secure, auditable, deterministic execution environment** that guarantees:

-   No command injection\
-   No unexpected shell interpretation\
-   No variable or environment leakage\
-   No uncontrolled filesystem traversal\
-   No invisible execution pathways\
-   Full accountability through tamper‑resistant logs

### 2.2 Alpha Milestones

-   REPL cycle (Read → Parse → Validate → Execute → Log)\
-   Hardened parser\
-   Whitelist TOML model\
-   Kernel‑only execution\
-   Sanitized environment\
-   Restricted built‑in (`cd`)\
-   Syslog integration at `LOG_AUTH`

------------------------------------------------------------------------

## 3. System Architecture

    src/
    ├── main.rs        # Interactive REPL and system coordination
    ├── config.rs      # TOML configuration loader (serde)
    ├── parser.rs      # Secure tokenizer and validator
    ├── executor.rs    # Direct kernel execution (std::process::Command)
    └── logger.rs      # Syslog audit integration (LOG_AUTH)

### 3.1 main.rs --- REPL Orchestrator

-   interactive loop\
-   uniform error handling\
-   dispatches parsing, execution and logging\
-   ensures no panic conditions under malformed input

### 3.2 config.rs --- Configuration Layer

-   parses `rules.toml`\
-   immutable command rule set\
-   support for per‑user rules (`allow_users`)\
-   strict flag matching and path verification

### 3.3 parser.rs --- Hardened Tokenizer

Security mechanisms include:

-   ASCII‑safe token validation\
-   blocking of separators (`;`, `|`, `||`, `&`, `&&`, `$()`, backticks,
    redirections)\
-   canonical path verification\
-   symlink traversal protection\
-   forbidden use of `..` and absolute paths in `cd`\
-   strict argument/flag whitelisting

Outputs: a validated, normalized `ValidatedCommand`.

### 3.4 executor.rs --- Kernel‑Only Execution

Execution is performed through:

-   `std::process::Command` (fork/exec)\
-   arguments passed strictly as `argv[]`\
-   **no shell interpretation at any stage**\
-   environment cleared and rebuilt with:

```{=html}
<!-- -->
```
    PATH=/bin:/usr/bin
    LANG=C
    LC_ALL=C
    HOME=/home/restrito

### 3.5 logger.rs --- Non‑Repudiation Layer

-   logs to `LOG_AUTH` facility\
-   records: timestamps, UID, raw attempt, result\
-   restricted user cannot access log subsystem\
-   compatible with rsyslog rotation and retention

------------------------------------------------------------------------

## 4. MVP Commands (Alpha)

Supported only if listed in `rules.toml`:

-   `ls`
-   `pwd`
-   `cd` (internal, sandboxed)
-   `cat`
-   `mkdir`
-   `rmdir`
-   `echo`

Example TOML rule:

``` toml
[[command]]
name = "ls"
path = "/bin/ls"
allow_flags = ["-l", "-a"]
allow_users = ["1001"]
```

------------------------------------------------------------------------

## 5. Security Features (Alpha)

### 5.1 Injection Immunity

-   No shell operators\
-   No pipelines\
-   No substitution\
-   No redirections\
-   No control characters\
-   No ambiguous parsing

### 5.2 Built‑In `cd` Sandboxing

-   path canonicalization\
-   symlink integrity checks\
-   prevention of absolute paths\
-   disallows escaping via `..`

### 5.3 Sanitized Execution Environment

-   full ENV purge\
-   minimal, deterministic environment variables\
-   consistent execution regardless of user context

### 5.4 Auditing & Logging

-   all actions logged\
-   tamper‑resistant\
-   granular tracking per UID

------------------------------------------------------------------------

## 6. Roadmap

### 6.1 Alpha (Current)

-   Core REPL\
-   Parser hardening\
-   Whitelist engine\
-   Execution module\
-   Logger integration

### 6.2 Beta (Near Future)

-   secure variable system\
-   internal commands (help, history, diagnostics)\
-   advanced rule granularity (UID/GID policies)\
-   extended error telemetry

### 6.3 Release Candidate

-   controlled pipe support\
-   workflow‑based execution\
-   containerized sandbox compatibility

### 6.4 Stable

-   optimization\
-   full documentation suite\
-   CI/CD matrix\
-   enterprise‑grade policy extensions

------------------------------------------------------------------------

## 7. License

Licensed under the MIT License.

------------------------------------------------------------------------

## 8. Author & Credits

**Marcelo da Silva Trindade (marcelositr)**\
Security-focused design, Rust engineering, and system integration.

**AI-Assisted Development:** Documentation, architecture support, and
structuring assisted by large‑scale model guidance.

------------------------------------------------------------------------
