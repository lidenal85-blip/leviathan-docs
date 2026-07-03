# 🧠 Pelleto AI

**Path:** `/opt/pelleto-ai` | **Priority:** P0 🔴 Critical

## Overview

AI assistant running on Termux (Android) with 23 MCP tools across 7 groups. Connects to Leviathan MCP server externally and provides Android-specific capabilities.

## Architecture

```
┌─────────────────────────────────────┐
│          Pelleto AI                 │
│  (Android/Termux Environment)       │
│                                     │
│  ┌──────────────────────────────┐  │
│  │  MCP Server (FastMCP)        │  │
│  │  ├─ Group 1: Termux          │  │
│  │  ├─ Group 2: Android         │  │
│  │  ├─ Group 3: Vibe Coding     │  │
│  │  ├─ Group 4: Network         │  │
│  │  ├─ Group 5: Database        │  │
│  │  ├─ Group 6: Torrent         │  │
│  │  └─ Group 7: Files           │  │
│  └──────────────────────────────┘  │
│                                     │
│  ➜ Connects to leviathan MCP       │
│  ➜ HTTP:8132 (SSE)                 │
└─────────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.x |
| **Framework** | FastMCP |
| **Platform** | Termux (Android) |
| **Port** | 8132 (SSE) |
| **Integration** | Leviathan MCP (HTTP:8300) |

## Key Metrics

- **23 tools** across 7 functional groups
- **External MCP connection** to leviathan (78.17.24.96:8300)
- **Android-specific** — wake lock, TTS, clipboard

## Strengths ✅

1. **Android integration** — unique mobile capabilities
2. **23 tools** — 2nd largest MCP toolset after leviathan
3. **Torrent management** — unique BitTorrent support

## Weaknesses ❌

| Issue | Priority | Fix ETA |
|-------|----------|---------|
| 🔴 Hardcoded Bearer token | Critical | 30min |
| 🟡 Public IP exposure | High | 2h |
| 🟢 No HTTPS | Medium | 1h |

## Dependencies

- Leviathan MCP server (HTTP:8300)
- Termux environment

---
*Built with ❤️ by LEVIATHAN Team*

## Full Audit

[📊 Полный аудит проекта](../analysis/audit/pelleto_ai_audit/)
