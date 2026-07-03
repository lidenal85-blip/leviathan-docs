# lev-status — System Status

Показывает состояние системы: RAM, SWAP, LOAD, DISK, активные сервисы.

## Usage

```bash
lev-cli status [--json]
```

## Metrics

| Metric | Source |
|--------|--------|
| RAM | `free -h` |
| SWAP | `free -h` |
| LOAD | `uptime` |
| DISK | `df -h /` |
| Services | systemctl |

## Location

`/opt/leviathan-docs/tools/lev-status`
