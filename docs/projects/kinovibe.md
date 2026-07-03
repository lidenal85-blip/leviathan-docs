# 🎬 Kinovibe

**Path:** `/var/www/kinovibe` | **Priority:** P0 🔴 Critical

## Overview

Movie discovery platform — a web application for browsing, searching, and discovering movies. Serves users via Nginx reverse proxy.

## Architecture

```
┌──────────────────────────────────┐
│            Kinovibe              │
│                                  │
│  User ──► Nginx ──► App          │
│                                  │
│  Stack: PHP/JS Frontend          │
│  Database: MySQL/PostgreSQL      │
└──────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Frontend** | JavaScript, HTML/CSS |
| **Backend** | PHP |
| **Web Server** | Nginx |
| **Deployment** | `/var/www/` |

## Strengths ✅

1. **Public-facing** — serves real users
2. **Nginx-backed** — production-ready web serving
3. **Active project** — regular updates

## Weaknesses ❌

| Issue | Priority | Fix ETA |
|-------|----------|---------|
| 🟡 No CI/CD | High | 2h |
| 🟢 Add monitoring | Medium | 1h |

## Dependencies

- Nginx (web server)
- Database (PostgreSQL/MySQL)

---
*Built with ❤️ by LEVIATHAN Team*

## Full Audit

[📊 Полный аудит проекта](../analysis/audit/kinovibe_audit/)
