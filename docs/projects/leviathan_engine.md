# 🚂 Leviathan Engine

**Path:** `/opt/leviathan_engine` | **Priority:** P0 🔴 Critical

## Overview

Central AI orchestrator of the LEVIATHAN platform. Manages AI agents, MCP tools, task execution, and system health. The brain of the entire operation.

## Architecture

```
┌─────────────────────────────────────────────┐
│            leviathan_engine                  │
│                                              │
│  ┌──────────────┐  ┌──────────────────────┐ │
│  │  Core        │  │  Agent Service       │ │
│  │  ├─ base_agent│  │  ├─ agent/          │ │
│  │  ├─ llm_factory│  │  ├─ mcp_server/    │ │
│  │  ├─ key_pool  │  │  ├─ claude_manager/ │ │
│  │  └─ orchestrator│ └──┴─ pool/           │ │
│  └──────────────┘  └──────────────────────┘ │
│                                              │
│  ┌──────────────┐  ┌──────────────────────┐ │
│  │  MCP Server  │  │  HQ (Brain)          │ │
│  │  ├─ 27 tools │  │  ├─ MASTER.md        │ │
│  │  ├─ HTTP:8300 │  │  ├─ projects/       │ │
│  │  └─ FastMCP  │  │  └─ sessions/       │ │
│  └──────────────┘  └──────────────────────┘ │
└─────────────────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12 |
| **Framework** | FastMCP, FastAPI |
| **AI** | Claude API, Gemini API, OpenAI |
| **Database** | SQLite (MCP context), PostgreSQL |
| **Transport** | HTTP SSE, stdio |
| **Deployment** | Systemd, uvicorn |

## Key Metrics

- **~27 MCP tools** available
- **5 subdirectories:** core, agent_service, MCP, HQ, db
- **1 systemd service:** `leviathan_mcp.service`
- **~50,000+ LOC** (estimated)

## Strengths ✅

1. **Comprehensive MCP implementation** — 27 tools across 6 modules
2. **Multi-LLM support** — Claude + Gemini + OpenAI
3. **Context Keeper** — SQLite-based session persistence
4. **Rich tool ecosystem** — fs, net, sys, design, data, ctx

## Weaknesses ❌

| Issue | Priority | Fix ETA |
|-------|----------|---------|
| 🔴 Root-level security | Critical | 2h |
| 🟡 Legacy `leviathan_mcp.py` duplication | High | 1h |
| 🟡 No rate limiting on lev_bash | High | 30min |
| 🟢 SQLite WAL unbounded growth | Medium | 15min |

## Dependencies

- `den4ik-claude` — Claude API access
- `secrets_vault` — Credentials
- `PostgreSQL` — User data
- `Redis` — Caching

## Roadmap

| Phase | Items |
|-------|-------|
| **Security** | Root user → dedicated MCP user |
| **Merge** | Consolidate legacy `leviathan_mcp.py` |
| **Rate limiting** | Add middleware for lev_bash |

---
*Built with ❤️ by LEVIATHAN Team*

## Full Audit

[📊 Полный аудит проекта](../analysis/audit/leviathan_engine_audit/)
