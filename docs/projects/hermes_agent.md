# 🧘 Hermes Agent

**Path:** `/opt/hermes_agent_venv` | **Priority:** P0 🔴 Critical

## Overview

Autonomous AI orchestration agent with MCP client capabilities. Can discover and use tools from any MCP server.

## Architecture

```
┌──────────────────────────────────┐
│         Hermes Agent             │
│                                  │
│  ├─ Skills System                │
│  │  ├─ native-mcp (MCP client)  │
│  │  ├─ fastmcp (server creator) │
│  │  └─ mcporter (CLI manager)   │
│  ├─ Task execution               │
│  └─ Agent loop                   │
└──────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12 |
| **MCP Client** | native-mcp |
| **Config** | `~/.hermes/config.yaml` |

## Strengths ✅

1. **MCP client** — automatically discovers server tools
2. **Skill system** — extensible plugin architecture
3. **FastMCP support** — can create new servers on-the-fly

---

## Full Audit

[📊 Полный аудит проекта](../analysis/audit/hermes_agent_audit/)
