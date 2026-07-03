# lev-health — Healthcheck

Проверяет доступность ключевых сервисов и сетевых портов.

## Usage

```bash
lev-cli health [--json]
```

## Checks

### Services
- leviathan-hq, leviathan-daemon, den4ik-claude
- arbitr_cockpit, savdabot, pelleto
- nginx, redis-server

### Ports
- 80 (HTTP), 443 (HTTPS)
- 5434 (PostgreSQL), 6379 (Redis)
- 8140 (SavdaBot), 8400 (SecretsVault)

## Location

`/opt/leviathan-docs/tools/lev-health`
