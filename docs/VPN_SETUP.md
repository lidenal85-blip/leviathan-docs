# 🌐 VPN Setup — LEVIATHAN Server

**Дата:** 2026-07-03 | **Статус:** Active

## Configuration

| Параметр | Значение |
|----------|----------|
| **Сервер** | 78.17.24.96 |
| **Панель** | 3x-ui (Xray 26.4.25) |
| **Протокол** | VLESS + Reality + Vision |
| **Порт** | 8443 (xray) |
| **SNI** | dl.google.com:443 |
| **Панель URL** | https://78.17.24.96:29585/kEsd2nr6jIgBKagrKP/ |
| **Admin** | admin (пароль в Vault :8400, ключ: x-ui-admin-password) |

## Inbounds

| Протокол | Статус | Порт |
|----------|--------|------|
| VLESS+Reality+Vision | ✅ Active | 8443 |
| Trojan | ❌ Not configured | — |

## Security

- ✅ Password changed from default (2026-07-03)
- ✅ Backup: `/etc/x-ui/x-ui.db.bak.20260703_103443`
- ❌ Trojan inbound not configured (add via web UI)
- ❌ fail2ban not configured for panel

## Client Setup

- **Android:** v2rayNG, Streisand, Hiddify
- **iOS:** V2Box, Streisand
- **Windows:** v2rayN, NekoRay
- **macOS:** V2Box, Streisand

---
*Документация: 7.md | LEVIATHAN Server*
