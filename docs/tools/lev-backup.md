# lev-backup — Project Backup

Архивирует ключевые проекты LEVIATHAN в `/root/backups/`.

## Usage

```bash
lev-cli backup [--json] [--dry-run]
```

## Backed Up Projects

- `/opt/leviathan_engine`
- `/opt/den4ik-claude`
- `/opt/arbitr_cockpit`
- `/opt/savdabot`
- `/opt/diet_platform`
- `/opt/pelleto-ai`
- `/opt/triton_agent`
- `/opt/secrets_vault`
- `/root/ai-interior-composer`
- `/var/www/kinovibe`
- `/opt/fri-messenger`

## Notes

- Excludes `.venv`, `node_modules`, `.git`
- Each backup is a `.tar.gz` with timestamp
- Max 30s per backup

## Location

`/opt/leviathan-docs/tools/lev-backup`
