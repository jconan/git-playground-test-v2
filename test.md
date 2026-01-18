# Claude Code Configuration Structure

This document explains how Claude Code instructions are organized across different environments.

## The Problem

We need different platform commands for host (macOS) vs devcontainer (Linux):

| Action     | macOS      | Linux      |
| ---------- | ---------- | ---------- |
| Open URL   | `open`     | `xdg-open` |
| Timeout    | `gtimeout` | `timeout`  |
| Clipboard  | `pbcopy`   | `xclip`    |

## Solution: Shared + Local Files

```mermaid
flowchart TB
    subgraph committed["Committed to repo"]
        root["CLAUDE.md<br/>Project rules"]
        dev[".devcontainer/CLAUDE.md<br/>Linux-specific"]
    end

    subgraph dotclaude[".claude/ directory"]
        shared["CLAUDE.md → shared rules"]
        local["CLAUDE.local.md<br/>(gitignored)"]
    end

    local -.->|"symlink in devcontainer"| dev

    style local fill:#ffd,stroke:#333,stroke-dasharray: 5 5
```
Current vs Fixed

```mermaid
flowchart LR
    subgraph wrong["❌ Current"]
        A[".claude/CLAUDE.md"] -->|symlink| B[".devcontainer/CLAUDE.md"]
    end

    subgraph right["✅ Fixed"]
        C[".claude/CLAUDE.local.md"] -->|symlink| D[".devcontainer/CLAUDE.md"]
    end

    style A fill:#fbb
    style C fill:#bfb,stroke-dasharray: 5 5
```

How It Works

- .claude/CLAUDE.md — Shared rules for all environments (committed)
- .claude/CLAUDE.local.md — Environment-specific overrides (gitignored)
  - Devcontainer: symlink → .devcontainer/CLAUDE.md
  - Host: could point to a macOS-specific file