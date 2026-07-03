# 🔐 Secrets Vault

**Path:** `/opt/secrets_vault` | **Priority:** P0 🔴 Critical

## Overview

Centralized secrets management — stores API keys, credentials, tokens for all LEVIATHAN projects.

## Architecture

```
┌──────────────────────────────────┐
│         Secrets Vault            │
│                                  │
│  ├─ Encrypted storage           │
│  ├─ API (port 8400)             │
│  └─ Access control              │
└──────────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12 |
| **Port** | 8400 |
| **Encryption** | AES/hardware |

## Security Note

⚠️ Audit revealed hardcoded tokens in configs. See MCP_AUDIT for details.

---
