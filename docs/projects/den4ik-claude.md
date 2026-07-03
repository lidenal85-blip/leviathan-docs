# 🤖 Den4ik Claude

**Path:** `/opt/den4ik-claude` | **Priority:** P0 🔴 Critical

## Overview

Claude AI session manager — manages a pool of Claude accounts, handles authentication, session persistence, and API routing. Powers all Claude interactions across the platform.

## Architecture

```
┌────────────────────────────────────┐
│          den4ik-claude             │
│                                    │
│  ┌──────────────────────────────┐ │
│  │  Claude Manager              │ │
│  │  ├─ Account Pool (session)   │ │
│  │  ├─ Auth (login/cookies)     │ │
│  │  ├─ Project Executor         │ │
│  │  └─ Task Planner             │ │
│  └──────────────────────────────┘ │
│                                    │
│  Databases:                        │
│  ├─ claude_accounts.db            │
│  ├─ claude_projects.db            │
│  └─ claude_sessions.db            │
└────────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12 |
| **Database** | SQLite |
| **Transport** | REST API (port 8200) |
| **Auth** | Cookie-based session management |
| **Deployment** | Systemd |

## Key Metrics

- **Multiple Claude accounts** managed
- **SQLite databases:** accounts, projects, sessions
- **Integration with** leviathan_engine agent_service
- **Active sessions** tracked per account

## Strengths ✅

1. **Multi-account pooling** — distributes load across Claude accounts
2. **Session persistence** — cookies and auth stored in SQLite
3. **Project tracking** — per-project session isolation
4. **Task planning** — built-in task planner for complex prompts

## Weaknesses ❌

| Issue | Priority | Fix ETA |
|-------|----------|---------|
| 🔴 Cookie expiration handling | Critical | 1h |
| 🟡 No load balancing across accounts | High | 2h |
| 🟢 SQLite concurrent access | Medium | 30min |

## Dependencies

- **Provides** Claude API access to `leviathan_engine`
- **Depends on** `secrets_vault` for credentials

---
*Built with ❤️ by LEVIATHAN Team*

## Full Audit

[📊 Полный аудит проекта](../analysis/audit/den4ik_claude_audit/)
