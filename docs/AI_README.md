# 🤖 AI Agent Guide — leviathan-docs

> This file helps AI agents navigate the LEVIATHAN documentation.

## Overview

**Site:** https://lidenal85-blip.github.io/leviathan-docs/  
**Repo:** https://github.com/lidenal85-blip/leviathan-docs  
**Stack:** MkDocs 1.6.1 + Material for MkDocs  
**Server:** 78.17.24.96 (LEVIATHAN)

## Documentation Structure

```
docs/
├── index.md              ← Главная страница (точка входа)
├── IMPLEMENTATION_LOG.md ← Лог реализации
├── AI_README.md          ← Этот файл (гид для AI-агентов)
├── analysis/
│   ├── index.md          ← Сводка аналитики
│   ├── MCP_AUDIT_2026-07-03.md
│   ├── *AUDIT_2026-07-03.md  ← 6 аналитических отчётов
│   └── audit/            ← 60 аудитов проектов
├── projects/
│   ├── INDEX.md          ← Каталог 58 проектов
│   ├── OVERVIEW.md       ← Архитектура платформы
│   ├── INTERACTION_MAP.md ← Карта зависимостей
│   ├── DECISIONS.md      ← ADR записи
│   └── *.md              ← По проекту на файл
├── tools/
│   ├── index.md          ← CLI инструменты
│   └── lev-*.md          ← Документация по каждому инструменту
├── context/              ← Контекстные документы
└── prompts/              ← Системные промпты
```

## Key Files for AI Agents

| File | Purpose | Link |
|------|---------|------|
| `index.md` | Главная страница, таблица состояния | [/](/leviathan-docs/) |
| `projects/INDEX.md` | Каталог всех 58 проектов | [/projects/INDEX/](/leviathan-docs/projects/INDEX/) |
| `projects/OVERVIEW.md` | Архитектура с ASCII-диаграммой | [/projects/OVERVIEW/](/leviathan-docs/projects/OVERVIEW/) |
| `analysis/MCP_AUDIT_2026-07-03.md` | MCP инфраструктурный аудит | [/analysis/MCP_AUDIT_2026-07-03/](/leviathan-docs/analysis/MCP_AUDIT_2026-07-03/) |
| `IMPLEMENTATION_LOG.md` | Лог всех изменений | [/IMPLEMENTATION_LOG/](/leviathan-docs/IMPLEMENTATION_LOG/) |

## Navigation Commands

```bash
# Quick status
curl -s https://lidenal85-blip.github.io/leviathan-docs/ | head -50

# Get project list
curl -s https://lidenal85-blip.github.io/leviathan-docs/projects/INDEX/ | grep 'P0'

# Check build
cd /opt/leviathan-docs && python3 -m mkdocs build 2>&1 | tail -5
```

## Platform References

- [🗺️ Master Roadmap](MASTER_ROADMAP.md) — Source of truth for architecture
- [✅ Session Checklist](SESSION_CHECKLIST.md) — Anti-Chaos protocol
- [🖥️ Server Metrics](SERVER_METRICS_COMPARISON.md) — Real vs stated metrics

## Server Info (for AI context)

- **IP:** 78.17.24.96
- **RAM:** 3.8Gi (1.9Gi used)
- **Services:** 22 active systemd units
- **Projects:** 58 monitored
- **Git:** `origin/main` → gh-pages auto-deploy

---
*Generated for AI agents — 2026-07-03*
