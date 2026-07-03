# ⚙️ АУДИТ: Leviathan Engine (ЯДРО)

**Путь:** /opt/leviathan_engine/
**Размер:** 144 MB
**Tier:** 1 (Критичный)
**Сервис:** 4 systemd (daemon, agent, mcp, hq)
**Git:** ✅ github.com/den4ikorm1985/leviathan-engine.git
**README:** ❌
**Тесты:** ❌

## Стек
Python, FastAPI, SQLite, LLM (Gemini, Groq)

## Архитектура
- agent/ — ядро агента (25K LOC)
- mcp_server/ — MCP сервер
- heads/ — EXECUTOR, REASONER, MNEME, HERALD
- execution/ — пайплайны
- db/ — SQLite (контекст, сессии)

## Статус
🟢 Active — 4 systemd сервиса

## 🚨 Проблемы
- 🔴 Лицензия DEMO-SOTA-2026 истекла 01.07.2026
- 🔴 Нет README
- 🔴 Нет тестов
- 🟡 Secrets в os.environ (Vault cutover не завершён)

## Оценка: 6/10 🟡
