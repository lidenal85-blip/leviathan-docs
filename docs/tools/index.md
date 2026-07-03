# 🛠️ LEVIATHAN CLI Tools

Набор CLI-утилит для управления платформой LEVIATHAN.

## Установка

Инструменты находятся в `/opt/leviathan-docs/tools/` и доступны через симлинки в `/usr/local/bin/`:

```bash
ls -la /usr/local/bin/lev-*
```

## Инструменты

| Команда | Описание |
|---------|----------|
| `lev-cli` | Главный CLI — точка входа для всех команд |
| `lev-status` | Статус системы — RAM, SWAP, LOAD, DISK, сервисы |
| `lev-health` | Healthcheck — проверка сервисов и портов |
| `lev-audit` | Аудит проектов — список с Tier-классификацией |
| `lev-backup` | Бэкап проектов — архивация в /root/backups/ |

## Использование

```bash
# Статус системы
lev-cli status
lev-cli status --json

# Healthcheck
lev-cli health
lev-cli health --json

# Аудит проектов
lev-cli audit
lev-cli audit --json

# Бэкап
lev-cli backup
lev-cli backup --json
lev-cli backup --dry-run
```
