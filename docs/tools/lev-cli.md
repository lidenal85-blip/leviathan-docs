# lev-cli — Main CLI

Главная точка входа для всех команд LEVIATHAN.

## Usage

```bash
lev-cli [command] [options]
```

## Commands

| Command | Description |
|---------|-------------|
| `status` | System status (RAM, SWAP, LOAD, DISK, services) |
| `health` | Healthcheck of all services and ports |
| `audit` | Project audit with Tier classification |
| `backup` | Backup all projects |

## Global Options

| Option | Description |
|--------|-------------|
| `--help` | Show help |
| `--json` | Output in JSON format |

## Examples

```bash
lev-cli status
lev-cli status --json
lev-cli health
lev-cli backup --dry-run
```

## Location

`/opt/leviathan-docs/tools/lev-cli`
