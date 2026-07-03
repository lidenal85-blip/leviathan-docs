# 🤖 SavdaBot

**Path:** `/opt/savdabot` | **Priority:** P0 🔴 Critical

## Overview

Multi-functional Telegram bot providing services to users. One of the key revenue-generating services on the platform.

## Architecture

```
┌──────────────────────────────────┐
│            SavdaBot              │
│                                  │
│  ┌──────────────────────┐       │
│  │  Telegram Bot Handler│       │
│  │  ├─ Messages       │       │
│  │  ├─ Commands       │       │
│  │  └─ Inline Queries │       │
│  └──────────────────────┘       │
│                                  │
│  Backend: Python + telegram API  │
│  Port: 8140                      │
└──────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12 |
| **Framework** | python-telegram-bot |
| **Port** | 8140 |
| **Deployment** | Systemd |

## Strengths ✅

1. **Revenue-generating** — active user base
2. **Telegram-native** — leverages Telegram ecosystem
3. **Stable operation** — monitored via lev-health

## Weaknesses ❌

| Issue | Priority | Fix ETA |
|-------|----------|---------|
| 🟡 No Web dashboard | High | 4h |
| 🟢 Limited logging | Medium | 30min |

## Dependencies

- Telegram Bot API
- leviathan_engine (for AI tasks)

---
*Built with ❤️ by LEVIATHAN Team*
