# 🏗️ ПЛАН СЛИЯНИЯ АГЕНТНЫХ СИСТЕМ
## Архитектурное объединение Leviathan Engine + Triton + Pelleto + den4ik-claude

**Дата:** 2026-07-02
**Сервер:** LEVIATHAN (78.17.24.96)
**Статус:** DRAFT — Требует утверждения
**Автор:** BUFFY (Architecture Analysis)

---

## 📋 1. Executive Summary

### Проблема
На сервере работает **6+ независимых агентных систем**, каждая со своей архитектурой, конфигурацией и точками входа. Это приводит к:
- ❌ Дублированию кода (LLM-провайдеры, модели данных, инструменты)
- ❌ Раздуванию памяти (несколько Python-процессов)
- ❌ Отсутствию единого управления (разные systemd-сервисы)
- ❌ Невозможности роутинга задач между агентами
- ❌ Разным уровням безопасности и observability

### Решение
Построить **единую Leviathan Agent Platform (LAP)**, которая объединит:
1. **Ядро (Manifest 5.1.0 + ADR-002)** — runtime decision-making kernel
2. **Шлюз** — единая точка входа (CLI / API / MCP / Telegram)
3. **Агент-регистр** — все специализированные агенты как подключаемые модули
4. **Единые сервисы** — LLM-маршрутизация, память, безопасность, observability

---

## 2. 🔍 Текущий ландшафт

| Система | Расположение | Язык | Строк кода | Статус | Назначение |
|---------|-------------|------|-----------|--------|------------|
| **Leviathan Engine** | `/opt/leviathan_engine/agent_service/` | Python | ~25,000 | 🟢 Production | Основной агент (инструменты, MCP, Telegram) |
| **Triton Agent** | `/opt/triton_agent/` | Python | ~3,900 | 🟢 Production | Спорт-обозреватель (футбол) |
| **Pelleto AI** | `/opt/pelleto-ai/` | Python | ~5,000 | 🟡 MCP broken | Общий AI-агент |
| **den4ik-claude** | `/opt/den4ik-claude/` | Python | ~125,000 | 🟢 Production | Telegram-бот (монолит) |
| **ArbitrCockpit** | `/opt/arbitr_cockpit/` | Python | ~8,000 | 🟢 Production | Панель управления |
| **Freebuff** | `/root/freebuff/` | Python | ~2,000 | 🔴 Crashing | CLI-аудитор |
| **free-claude-code** | `/root/free-claude-code/` | Python | ~10,000 | 🟡 External | Внешний проект |

### 2.1 Текущие точки интеграции

```
┌──────────────┐  ┌────────────────┐  ┌─────────────────┐
│  Leviathan   │  │    Triton      │  │   den4ik-claude │
│  :8200, 8300 │  │    :8201       │  │   (Telegram)    │
│  Agent + MCP │  │  FastAPI + Bot │  │   Pyrogram      │
└──────┬───────┘  └───────┬────────┘  └───────┬─────────┘
       │                  │                   │
       │         ┌────────┴────────┐          │
       │         │  LLM Gateway    │          │
       │         │  Groq→Gemini→   │          │
       │         │  →Leviathan     │          │
       │         └─────────────────┘          │
       └──────────────────┬───────────────────┘
                          │
              ┌───────────┴───────────┐
              │    EventBus (pub/sub)  │
              │    task_queued/started │
              │    /done/failed        │
              └───────────────────────┘
```

### 2.2 Дублирование компонентов

| Компонент | Leviathan | Triton | den4ik-claude | Pelleto |
|-----------|-----------|--------|---------------|---------|
| LLM-провайдеры | ✅ providers.py | ✅ LLMGateway | ✅ Встроен | ✅ Встроен |
| Telegram Bot | ✅ tg_bot.py | ✅ bot/ | ✅ bot.py (125K) | ❌ |
| FastAPI API | ✅ api/ | ✅ delivery/miniapp | ❌ | ✅ |
| База данных | ✅ SQLite (4 БД) | ✅ InMemory+PG | ✅ SQLite | ❌ |
| Circuit Breaker | ❌ | ✅ В LLMGateway | ❌ | ❌ |
| Memory System | ✅ V2 (5 типов) | ❌ | ❌ | ✅ Knowledge Base |
| Auth/Security | ✅ PII Detection | ❌ | ❌ | ❌ |

---

## 3. 🏛️ ЦЕЛЕВАЯ АРХИТЕКТУРА: LEVIATHAN AGENT PLATFORM (LAP)

### 3.1 Принципы дизайна

1. **Single Kernel** — одно ядро (runtime decision-making из ADR-002), все агенты — плагины
2. **Shared Services** — LLM-роутинг, память, безопасность — единые сервисы
3. **No Monoliths** — den4ik-claude разделяется на модули
4. **Multi-Interface** — один backend, много frontends (CLI, API, MCP, TG, Web)
5. **Runtime Hot-Plug** — агенты можно добавлять/удалять без перезапуска
6. **Cost-Aware** — единый budget-management для всех LLM-вызовов
7. **Hybrid Mode** — pipeline (старый) и runtime (новый) режимы сосуществуют

### 3.2 Схема целевой архитектуры

```
┌──────────────────────────────────────────────────────────────────────────┐
│                     LEVIATHAN AGENT PLATFORM (LAP)                       │
│                                                                          │
│  ┌──────────────────── INTERFACE LAYER ──────────────────────────────┐  │
│  │                                                                     │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐            │  │
│  │  │  CLI     │  │  HTTP    │  │   MCP    │  │ Telegram │            │  │
│  │  │ lev-cli  │  │  :8200   │  │  :8300   │  │  TG Bot  │            │  │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘            │  │
│  │       │              │             │              │                  │  │
│  │       └──────────────┴─────────────┴──────────────┘                  │  │
│  │                                                                     │  │
│  └──────────────────────────┬─────────────────────────────────────────┘  │
│                             │                                            │
│  ┌──────────────────────────▼─────────────────────────────────────────┐  │
│  │                    API GATEWAY / ROUTER                             │  │
│  │  Аутентификация → Rate Limiting → Маршрутизация → Логирование      │  │
│  └──────────────────────────┬─────────────────────────────────────────┘  │
│                             │                                            │
│  ┌──────────────────────────▼─────────────────────────────────────────┐  │
│  │                    EXECUTION KERNEL (ADR-002)                       │  │
│  │                                                                     │  │
│  │  ┌──────────────────────────────────────────────────────────────┐  │  │
│  │  │  AgentKernel (ScenarioPolicy → DecisionEngine → ExecutionLo  │  │  │
│  │  │              → ReplanningEngine → SafetyGuards)              │  │  │
│  │  └──────────────────────────────────────────────────────────────┘  │  │
│  │                                                                     │  │
│  │  ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐  │  │
│  │  │  Pipeline  │  │  Runtime   │  │  Parallel  │  │   Saga     │  │  │
│  │  │  Executor  │  │  Executor  │  │  Executor  │  │  Executor  │  │  │
│  │  │ (legacy)   │  │ (ADR-002)  │  │            │  │            │  │  │
│  │  └────────────┘  └────────────┘  └────────────┘  └────────────┘  │  │
│  └──────────────────────────┬─────────────────────────────────────────┘  │
│                             │                                            │
│  ┌──────────────────────────▼─────────────────────────────────────────┐  │
│  │                    AGENT REGISTRY                                   │  │
│  │  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌──────────┐     │  │
│  │  │General │  │ Sport  │  │Analyst │  │  TG    │  │ External │     │  │
│  │  │ Agent  │  │ Obsrv. │  │Arbitr  │  │  Bot   │  │(Pelleto) │     │  │
│  │  │(core)  │  │(Triton)│  │Cockpit │  │Agent   │  │          │     │  │
│  │  └────────┘  └────────┘  └────────┘  └────────┘  └──────────┘     │  │
│  └──────────────────────────┬─────────────────────────────────────────┘  │
│                             │                                            │
│  ┌──────────────────────────▼─────────────────────────────────────────┐  │
│  │                    SHARED SERVICES                                  │  │
│  │                                                                     │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌─────────┐  │  │
│  │  │  LLM     │ │ Memory   │ │ Security │ │  Event   │ │ Secrets │  │  │
│  │  │  Router  │ │ System   │ │ Firewall │ │   Bus    │ │  Vault  │  │  │
│  │  │ cost-    │ │ V2 (5    │ │ PII+RBAC │ │ Durable  │ │ AES-256 │  │  │
│  │  │ aware    │ │ types)   │ │          │ │ +DLQ     │ │  :8400  │  │  │
│  │  └──────────┘ └──────────┘ └────────┘ └──────────┘ └─────────┘  │  │
│  │                                                                     │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐              │  │
│  │  │Observ.   │ │Checkpoint│ │Scheduler │ │Sandbox   │              │  │
│  │  │OTEL+Cost │ │SQLite/   │ │Cron/Delay│ │Docker    │              │  │
│  │  │Tracking  │ │Postgres  │ │          │ │Isolation │              │  │
│  │  └──────────┘ └──────────┘ └──────────┘ └──────────┘              │  │
│  └─────────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────┘
```

### 3.3 Модульная структура кода

```
/opt/leviathan_engine/agent_service/
├── lap/                              # 🆕 LEVIATHAN AGENT PLATFORM ROOT
│   ├── __init__.py
│   ├── kernel/                       # ADR-002 ядро
│   │   ├── __init__.py
│   │   ├── loader.py                 # ScenarioPolicy Pydantic parser
│   │   ├── decision_engine.py        # DecisionEngine
│   │   ├── execution_loop.py         # ExecutionLoop
│   │   ├── replanner.py              # ReplanningEngine
│   │   └── safety_guards.py          # SafetyGuards (max_steps, cost, timeout)
│   ├── agents/                       # 🆕 Agent Registry
│   │   ├── __init__.py
│   │   ├── registry.py              # AgentRegistry (hot-plug)
│   │   ├── base.py                  # BaseAgent(Runnable) — единый интерфейс
│   │   ├── general/                 # → из agent/core.py
│   │   ├── sport_observer/          # → из /opt/triton_agent/
│   │   ├── arbitr/                  # → из /opt/arbitr_cockpit/
│   │   ├── tg_bot/                  # → из agent/tg_bot.py + den4ik-claude
│   │   └── pelleto/                 # → из /opt/pelleto-ai/
│   ├── interfaces/                   # 🆕 Interface Layer
│   │   ├── __init__.py
│   │   ├── cli.py                   # lev-cli (CLI entry point)
│   │   ├── mcp.py                   # MCP сервер (из mcp_server/)
│   │   ├── api.py                   # HTTP API (из api/)
│   │   └── telegram.py             # Telegram Bot (из den4ik-claude + tg_bot)
│   └── shared/                       # 🆕 Shared Services
│       ├── __init__.py
│       ├── llm_router.py            # ModelRouter (cost/latency/fallback)
│       ├── cost_manager.py          # Budget management
│       └── gateway.py               # Unified LLM Gateway
│
│   ⚠️ Ниже — существующие модули, которые НЕ переезжают:
├── agent/          — остаётся как pipeline-режим (legacy)
├── mcp_server/     — расширяется / переезжает в lap/interfaces/mcp.py
├── middleware/     — переезжает в shared (станет глобальным)
├── callbacks/     — расширяется для всех агентов
├── workflow/      — становится частью lap/kernel/
├── db/            — остаётся, расширяется shared-сервисами
├── heads/         — переезжает в lap/agents/
├── execution/     — переезжает в shared (pipeline_log, backoff, idempotency)
│
├── main.py        — упрощается: только загрузка LAP
└── event_bus.py   — расширяется до DurableEventBus
```

---

## 4. 🔄 ПЛАН МИГРАЦИИ: 4 ФАЗЫ

### Фаза 0: Подготовка (1-2 дня)
**Цель:** Создать каркас LAP без нарушения существующей работы

| Шаг | Действие | Файлы | Кто |
|-----|----------|-------|-----|
| 0.1 | Создать директорию `lap/` с __init__.py | `lap/__init__.py` | Dev |
| 0.2 | Создать `BaseAgent(Runnable)` — единый интерфейс для всех агентов | `lap/agents/base.py` | Dev |
| 0.3 | Создать `AgentRegistry` — hot-plug регистр | `lap/agents/registry.py` | Dev |
| 0.4 | Создать `lap/shared/` с __init__.py | `lap/shared/__init__.py` | Dev |
| 0.5 | Создать `lap/interfaces/` с __init__.py | `lap/interfaces/__init__.py` | Dev |
| 0.6 | Написать тест: `BaseAgent → AgentRegistry → invoke` | `tests/test_lap_base.py` | Dev |

**Критерий приёмки:** Можно создать агента, зарегистрировать и выполнить через `invoke()`

---

### Фаза 1: Миграция Shared Services (3-5 дней)
**Цель:** Вынести общие сервисы из дублирующихся реализаций в единый shared-слой

| Шаг | Действие | Источник | Цель |
|-----|----------|----------|------|
| 1.1 | **Unified LLM Gateway** | Triton `LLMGateway` + Leviathan `providers.py` + `model_router.py` | `lap/shared/gateway.py` |
| 1.2 | **Cost Manager** — budget enforcement | ADR-002 спецификация | `lap/shared/cost_manager.py` |
| 1.3 | **Durable EventBus** | `event_bus.py` + ADR-002 DurableEventBus | `lap/shared/event_bus.py` |
| 1.4 | **Circuit Breaker** | Triton `LLMGateway` → вынести в shared | `lap/shared/circuit_breaker.py` |
| 1.5 | **Memory System** → сделать глобальным | `db/memory_system.py` | `lap/shared/memory.py` |
| 1.6 | **Security** — объединить PII, RBAC, Firewall | `agent/tools_security.py` + ADR-002 | `lap/shared/security.py` |
| 1.7 | **Checkpoint Manager** | `mcp_server/lev_context.py` (ContextKeeper) | `lap/shared/checkpoint.py` |
| 1.8 | **Observability** — OTEL + Cost Tracking | ADR-002 + `pipeline_log.py` + `db/journal.py` | `lap/shared/observability.py` |

**Критерий приёмки:** Все 4 существующих агента могут использовать единый LLM Gateway

---

### Фаза 2: Миграция агентов (5-8 дней)
**Цель:** Перевести каждый агент на `BaseAgent` интерфейс и подключить к LAP

| Шаг | Действие | Откуда | Куда |
|-----|----------|--------|------|
| 2.1 | **General Agent** — обёртка `LeviathanAgent` в `BaseAgent` | `agent/core.py` | `lap/agents/general/` |
| 2.2 | **Sport Observer** — импорт Triton как плагина | `/opt/triton_agent/` | `lap/agents/sport_observer/` |
| 2.3 | **TG Bot Agent** — разделить den4ik-claude на модули | `/opt/den4ik-claude/bot.py` | `lap/agents/tg_bot/` |
| 2.4 | **Arbitr Cockpit** — обёртка в BaseAgent | `/opt/arbitr_cockpit/` | `lap/agents/arbitr/` |
| 2.5 | **Pelleto AI** — починить MCP + обёртка | `/opt/pelleto-ai/` | `lap/agents/pelleto/` |

**Критерий приёмки:** Все 5 агентов зарегистрированы в `AgentRegistry` и доступны через единый API

---

### Фаза 3: ADR-002 Kernel (5-7 дней)
**Цель:** Построить runtime decision-making kernel (ADR-002)

| Шаг | Действие | Спецификация |
|-----|----------|-------------|
| 3.1 | ScenarioPolicy Loader (Pydantic YAML) | ADR-002 Фаза 1 |
| 3.2 | DecisionEngine (Rule Filter + LLM Ranking + Cost Estimation) | ADR-002 Фаза 1 |
| 3.3 | ExecutionLoop (observe → decide → act → eval) | ADR-002 Фаза 1 |
| 3.4 | ReplanningEngine (adaptive plan changes) | ADR-002 Фаза 2 |
| 3.5 | SafetyGuards (max_steps, max_cost, timeout, circuit_breaker) | ADR-002 Фаза 2 |
| 3.6 | CLI команды (`lev-cli run scenario --mode PRO`) | ADR-002 Фаза 3 |

**Критерий приёмки:** `lev-cli run scenario freelance --mode PRO` запускает полный цикл

---

### Фаза 4: Интерфейсы и Production (3-5 дней)
**Цель:** Единые точки входа, документация, тесты

| Шаг | Действие |
|-----|----------|
| 4.1 | Unified CLI (`lev-cli`) — замена всех отдельных точек входа |
| 4.2 | Unified HTTP API (`:8200`) — OpenAI-compatible + Agent API |
| 4.3 | Unified MCP (`:8300`) — все инструменты всех агентов |
| 4.4 | Unified Telegram Bot — один бот, все агенты через роутинг |
| 4.5 | Тесты (unit + integration ≥ 70%) |
| 4.6 | Документация (ARCHITECTURE.md, API.md, CLI_GUIDE.md) |

**Критерий приёмки:** Одной командой `lev-cli run sport-observer` запускается Triton через LAP

---

## 5. 🧩 ДЕТАЛЬНАЯ СХЕМА ИНТЕГРАЦИИ АГЕНТОВ

### 5.1 BaseAgent Interface

```python
# lap/agents/base.py
class BaseAgent(Runnable[AgentTask, AgentResult]):
    """Единый интерфейс для всех агентов LAP."""
    
    agent_id: str                          # Уникальный ID
    agent_type: str                        # general | sport | arbitr | tg_bot | pelleto
    version: str                           # Семантическая версия
    capabilities: List[str]                # ["sport_observation", "seo", "arbitration"]
    
    # Жизненный цикл
    async def initialize(self) -> None     # Загрузка, инициализация БД
    async def invoke(self, task) -> Result  # Выполнение задачи
    async def health_check(self) -> Status  # Проверка здоровья
    async def shutdown(self) -> None       # Graceful shutdown
    
    # Метаданные
    def get_manifest(self) -> AgentManifest  # Манифест для регистра
    def get_metrics(self) -> Dict            # Метрики для observability
```

### 5.2 AgentRegistry

```python
# lap/agents/registry.py
class AgentRegistry:
    """Hot-plug регистр агентов."""
    
    async def register(self, agent: BaseAgent) -> None
    async def unregister(self, agent_id: str) -> None
    async def get(self, agent_id: str) -> BaseAgent
    async def find_by_capability(self, capability: str) -> List[BaseAgent]
    async def route(self, task: AgentTask) -> BaseAgent  # Авто-роутинг
    async def list_agents(self) -> List[AgentManifest]
    async def health_all(self) -> Dict[str, Status]
```

### 5.3 Схема роутинга задач

```
User Input → Intent Detection
    ├→ "напиши код"          → General Agent
    ├→ "последние матчи"     → Sport Observer (Triton)
    ├→ "статус арбитража"    → Arbitr Cockpit
    ├→ "отправь сообщение"   → TG Bot Agent
    └→ "создай дизайн"      → Pelleto AI
```

### 5.4 Унификация LLM Gateway

```python
# lap/shared/gateway.py — объединение providers.py + LLMGateway + model_router.py

class LLMGateway:
    """Единый шлюз к LLM с circuit breaker, cost tracking, fallback."""
    
    PROVIDERS = {
        "gemini": GeminiProvider,       # из agent/providers.py
        "groq": GroqProvider,           # из Triton LLMGateway
        "deepseek": DeepSeekProvider,   # новый
        "claude": ClaudeAdapter,        # из core_bridge/
    }
    
    async def complete(
        self,
        prompt: str,
        model: str = "auto",           # "auto" → ModelRouter
        budget: float = None,          # Cost-aware
        fallback: bool = True          # Circuit Breaker fallback
    ) -> ProviderResponse:
        ...
```

---

## 6. 🗓️ ОЦЕНКА ВРЕМЕНИ И ПРИОРИТЕТЫ

| Фаза | Дней | Приоритет | Зависимости | Результат |
|------|------|-----------|------------|-----------|
| **Фаза 0: Подготовка** | 1-2 | 🔴 P0 | Нет | Каркас LAP + BaseAgent |
| **Фаза 1: Shared Services** | 3-5 | 🔴 P0 | Фаза 0 | Единый LLM Gateway, EventBus, Security |
| **Фаза 2: Миграция агентов** | 5-8 | 🟡 P1 | Фаза 1 | Все агенты в LAP |
| **Фаза 3: ADR-002 Kernel** | 5-7 | 🟡 P1 | Фаза 2 | Runtime decision-making |
| **Фаза 4: Интерфейсы** | 3-5 | 🟢 P2 | Фаза 3 | CLI, API, MCP, TG unified |

**Итого:** ~17-27 рабочих дней до полного объединения

---

## 7. ⚠️ РИСКИ И ИХ МИТИГАЦИЯ

| Риск | Вероятность | Влияние | Митигация |
|------|-------------|---------|-----------|
| **Поломка production** при миграции | Средняя | Критическое | Фаза 0: всё в `lap/` без изменения существующего кода |
| **den4ik-claude (125K монолит)** | Высокая | Высокое | Постепенная декомпозиция: сначала обёртка, потом модуляризация |
| **Совместимость API** | Средняя | Среднее | Сохранить старые эндпоинты (:8200, :8300) как forward-совместимые |
| **Triton LLMGateway vs Leviathan providers** | Низкая | Среднее | Выбрать лучшую реализацию (Triton — circuit breaker, Leviathan — multi-provider) |
| **Freebuffd падает** | Высокая | Низкое | Не входит в LAP, лечится отдельно |

---

## 8. 📊 МЕТРИКИ УСПЕХА

| Метрика | До | После |
|---------|----|-------|
| **Количество Python-процессов** | 5+ | 1-2 (ядро + worker pool) |
| **Количество systemd-сервисов** | 4 (leviathan_agent, leviathan_mcp, den4ik-claude, sport-observer) | 2 (leviathan-core, leviathan-worker) |
| **Общее RAM** | ~1.5-2 GiB | ~500-800 MiB |
| **Время настройки нового агента** | 1-2 дня | 1-2 часа (реализовать BaseAgent) |
| **Duplicate code** | ~1500 строк | 0 строк |
| **Тестовое покрытие** | ~5% (только Triton) | ≥ 70% |

---

## 9. 📁 ФАЙЛЫ ДЛЯ ПЕРВОГО ШАГА (Фаза 0)

Следующие файлы — **первое, что нужно создать**:

```
/opt/leviathan_engine/agent_service/lap/__init__.py
/opt/leviathan_engine/agent_service/lap/agents/__init__.py
/opt/leviathan_engine/agent_service/lap/agents/base.py      — BaseAgent(Runnable)
/opt/leviathan_engine/agent_service/lap/agents/registry.py   — AgentRegistry
/opt/leviathan_engine/agent_service/lap/shared/__init__.py
/opt/leviathan_engine/agent_service/lap/interfaces/__init__.py
```

---

## 10. 📝 ЗАКЛЮЧЕНИЕ

Leviathan Agent Platform (LAP) — это не переписывание с нуля, а **архитектурная унификация** существующих наработок:

- **Manifest 5.1.0** дал формальный lifecycle, memory, workflow
- **ADR-002** даёт runtime decision-making kernel
- **LAP** склеивает всё в единую платформу

**Рекомендуемый следующий шаг:** Создать каркас Фазы 0 и обернуть один из агентов (например, Triton Sport Observer) в `BaseAgent`, чтобы проверить концепцию на реальном коде.

---

*Документ создан: 2026-07-02 | Leviathan Architecture Team*
