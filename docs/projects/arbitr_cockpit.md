# 📊 Arbitr Cockpit

**Path:** `/opt/arbitr_cockpit` | **Priority:** P0 🔴 Critical

## Overview

Business intelligence platform and order management system. Handles project complexity assessment (TC scoring), pipeline management, and business analytics.

## Architecture

```
┌─────────────────────────────────────┐
│          Arbitr Cockpit             │
│                                     │
│  ┌──────────┐  ┌──────────────────┐ │
│  │  LISA    │  │  Pipeline Engine │ │
│  │  TC Calc │  │  ├─ Stages      │ │
│  │  6 Axis  │  │  ├─ Orders      │ │
│  └──────────┘  │  └─ Status      │ │
│                └──────────────────┘ │
│                                     │
│  APIs: REST (port 8090)             │
│  Integration: MCP (arbitr_* tools)  │
└─────────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12 |
| **Framework** | FastAPI |
| **Database** | PostgreSQL |
| **Port** | 8090 |
| **MCP Tools** | `arbitr_lisa`, `arbitr_pipeline_status`, `arbitr_pipeline_advance` |

## Key Metrics

- **LISA TC scoring** — 6-axis complexity assessment
- **Pipeline management** — multi-stage order processing
- **3 MCP tools** exposed for AI agents
- **Nginx-enabled** via reverse proxy

## Strengths ✅

1. **LISA scoring system** — unique 6-axis project complexity analysis
2. **Pipeline automation** — automated stage transitions
3. **MCP integration** — AI agents can interact directly

## Weaknesses ❌

| Issue | Priority | Fix ETA |
|-------|----------|---------|
| 🟡 No monitoring dashboard | High | 3h |
| 🟢 No API versioning | Medium | 1h |

## Dependencies

- PostgreSQL (business data)
- leviathan_engine (MCP integration)
- Nginx (reverse proxy)

---
*Built with ❤️ by LEVIATHAN Team*

## Full Audit

[📊 Полный аудит проекта](../analysis/audit/arbitr_cockpit_audit/)
