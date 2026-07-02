# ПЛАН ВЫПОЛНЕНИЯ: Manifest 5.1 + GitHub Sync

**Сессия MCP:** `1782898455_leviathan_ag_Manifest_5.1_impleme`
**Дата:** 2026-07-01

---

## ПРИОРИТЕТЫ

### P0 — CRITICAL: Сохранить код перед изменениями

| # | Задача | Статус |
|---|--------|--------|
| 0.1 | GitHub push `leviathan_agent` (feature/claude-multi-account) | 🔄 Таймаут (большой репо) |
| 0.2 | GitHub push `leviathan-engine` (main) | 🔄 Таймаут |
| 0.3 | GitHub push `arbitr-cockpit` (master) | ⏳ Ожидает |
| 0.4 | Создать репо `secrets-vault` → git init + push | ✅ Репо создано, пуш ожидает |
| 0.5 | Создать ветку `manifest-5.1` в leviathan_agent | ⏳ Ожидает пуша |

### P1 — HIGHEST: Вшивание существующих файлов улучшений

| # | Задача | Файлы | Статус |
|---|--------|-------|--------|
| 1.1 | StateMachine в core.py: импорт, атрибут, переходы в run() | `core.py` + `improvements/agent_state.py` | ⏳ |
| 1.2 | Runnable Interface: наследование LeviathanAgent от Runnable | `core.py` + `improvements/runnable.py` | ⏳ |
| 1.3 | Middleware Pipeline: хуки BEFORE_MODEL, AFTER_MODEL, WRAP_TOOL_CALL | `core.py` + `improvements/middleware.py` | ⏳ |
| 1.4 | Dispatcher: интеграция PAUSED/RESUMED/INTERRUPTED | `dispatcher.py` | ⏳ |

### P2 — HIGH: Новые компоненты

| # | Задача | Путь | Статус |
|---|--------|------|--------|
| 2.1 | Callback System (CallbackManager с событиями) | `agent/callbacks/` | ⏳ |
| 2.2 | Workflow Engine (Branch/Merge/Parallel) | `agent/workflow/` | ⏳ |
| 2.3 | Memory System V2 (ShortTerm/LongTerm/Working) | `agent/memory/` | ⏳ |

### P3 — MEDIUM: GitHub полная синхронизация

| # | Задача | Статус |
|---|--------|--------|
| 3.1 | Push всех 15+ проектов в GitHub | ⏳ |
| 3.2 | Создать issues для критических улучшений | ⏳ |
| 3.3 | Обновить .claude/claude.md | ⏳ |

---

## GitHub Sync Plan

### Repositories to sync:

```
1. lidenal85-blip/Leviathan_Agent     → /opt/leviathan_engine/agent_service/   (feature/claude-multi-account)
2. den4ikorm1985/leviathan-engine      → /opt/leviathan_engine/                 (main)
3. den4ikorm1985/kinovibe              → /var/www/kinovibe/                     (master)
4. den4ikorm1985/voice-studio          → /var/www/voice_studio/                 (main)
5. den4ikorm1985/pelleto-ai            → /opt/pelleto-ai/                       (main)
6. den4ikorm1985/arbitr-cockpit        → /opt/arbitr_cockpit/                   (master)
7. lidenal85-blip/secrets-vault        → /opt/secrets_vault/                    (master) NEW
8. lidenal85-blip/diet_platform        → /opt/diet_platform/                    (main)
9. den4ikorm1985/kwork-dispatcher      → /opt/kwork_dispatcher/                 (main)
10. den4ikorm1985/vocal-bot            → /opt/vocal_bot/                        (main)
11. den4ikorm1985/citrus-aura          → /opt/citrus_aura/                      (master)
12. den4ikorm1985/fri-messenger        → /opt/fri-messenger/                    (main)
```

### Token management:
- GitHub tokens in remotes (embedded in URLs)
- Main token: `ghp_YOUR_TOKEN_HERE` (lidenal85-blip)
- Secondary: `ghp_YOUR_TOKEN_HERE` (den4ikorm1985)
- Token from git.md: `ghp_YOUR_TOKEN_HERE` (lidenal85-blip-alt)

### Branch strategy:
- `manifest-5.1` — от feature/claude-multi-account для разработки
- После завершения → merge в main/master
