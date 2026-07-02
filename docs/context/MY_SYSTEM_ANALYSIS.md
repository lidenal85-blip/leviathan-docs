# ГЛУБОКИЙ АНАЛИЗ АГЕНТНЫХ ПРОЕКТОВ НА СЕРВЕРЕ
**Дата:** 2026-07-01 | **Сессия:** `1782892229_leviathan_Глубокий_анализ_агент`
**Автор:** LEVIATHAN AGENT ANALYSIS

---

## 1. EXECUTIVE SUMMARY

На сервере `78.17.24.96` обнаружено **6 агентных проектов** и **10+ вспомогательных скриптов**. 
Основная кодовая база — **~245 Python файлов**, **~50K+ строк кода**.

### Ключевые выводы:

| Проект | Файлов | Строк кода | Статус |
|--------|--------|-----------|--------|
| **Leviathan Engine** | 121 | ~25,000 | 🟢 Production (основной) |
| **Triton Agent** | 45 | ~3,905 | 🟢 Production (спорт) |
| **Pelleto AI** | 37 | ~5,000 | 🟡 MCP недоступен |
| **den4ik-claude Bot** | 3 | ~125,000+ | 🟢 Production |
| **ArbitrCockpit** | 20+ | ~8,000 | 🟢 Production |
| **free-claude-code** | 30+ | ~10,000 | 🟡 Внешний проект |

### Оценка соответствия Манифесту 5.0.0: **9.5/20** ⚠️

**Сильные стороны:** Durable Execution ✅, Checkpointing ✅, EventBus ⚠️, Security ⚠️
**Слабые стороны:** Agent Lifecycle ❌, Runnable Interface ❌, Workflow Engine ❌, Plugin SDK ❌, Multi-Agent ❌

---

## 2. INVENTORY — ВСЕ НАЙДЕННЫЕ ПРОЕКТЫ

### 2.1 Leviathan Engine (Основной)
- **Расположение:** `/opt/leviathan_engine/agent_service/`
- **Порт:** 8200 (агент), 8300 (MCP)
- **Размер:** 27MB
- **Python файлов:** 121
- **Запущен:** `systemctl enable pelleto` (агент на порту 8131), MCP на 8300
- **Базы данных:** leviathan.db (18MB), leviathan_kb.db, context_memory.db, claude_accounts.db

#### Структура модулей:
```
agent_service/
├── agent/                    # Ядро агента (core.py, dispatcher.py, intent.py)
│   ├── core.py              # LeviathanAgent (781 строк) — основной цикл
│   ├── dispatcher.py        # Dispatcher + DispatcherModeState (364 строк)
│   ├── intent.py            # Intent Detection (160 строк)
│   ├── tools*.py            # 13 файлов инструментов (3149 строк)
│   ├── model_router.py      # Маршрутизация моделей
│   ├── providers.py         # Провайдеры LLM
│   └── tg_bot.py            # Telegram Bot с AgentRunner
├── mcp_server/              # MCP сервер (порт 8300)
│   ├── leviathan_mcp_server.py  # HTTP entrypoint (uvicorn)
│   ├── leviathan_mcp.py         # stdio/HTTP MCP обработчик
│   ├── lev_context.py           # ContextKeeper (SQLite сессии)
│   └── modules/                 # Модули: ctx.py, fs.py, data.py, sys_.py, ...
├── heads/                   # Специализированные головы
│   ├── herald/              # Система коммуникаций
│   ├── mneme/               # Контекст и память
│   └── reasoner/            # Оценка и анализ
├── execution/               # Pipeline выполнения
│   ├── backoff_scheduler.py # Backoff при 429
│   ├── idempotency.py       # OperationRegistry (SQLite)
│   ├── result_envelope.py   # Структурированные ответы
│   └── pipeline_log.py      # Логирование pipeline
├── db/                      # Система хранения
│   ├── context_memory.py    # SQLite память контекста
│   ├── context_ledger.py    # Журнал контекста
│   ├── journal.py           # ExecutionJournal
│   ├── knowledge_base.py    # База знаний
│   ├── storage.py           # Хранилище
│   ├── task_engine.py       # Task Engine
│   └── route_index.py       # Route Index
├── delivery/                # Каналы доставки
│   └── claude_accounts_web.py
├── core_bridge/             # Claude Adapter + Key Pool
├── api/                     # API (OpenAI Compatible)
├── claude_manager/          # Управление Claude сессиями
├── config/                  # Конфигурация
└── main.py                  # Точка входа (1036 строк)
```

### 2.2 Triton Agent (Sport Observer)
- **Расположение:** `/opt/triton_agent/`
- **Порт:** 8201
- **Размер:** 860KB
- **Python файлов:** 45 (3905 строк)
- **Запущен:** `main_prod.py --mode full --port 8201`

#### Структура модулей:
```
triton_agent/
├── core/                    # Ядро
│   ├── models/              # Pydantic модели (CanonicalSportEvent, MatchDTO)
│   ├── registry/            # StaticSportRegistry
│   ├── storage/             # MatchRepository (InMemory + Postgres)
│   ├── orchestrator/        # ContentOrchestrator
│   ├── notification/        # NotificationManager + UserPreferences
│   ├── validation/          # ContentValidator
│   └── scheduler.py         # Fоновый планировщик
├── sports/                  # Спортивные адаптеры
│   ├── base/                # ABI: SportDataProvider, SportRules, SportPresentation
│   └── football/            # Футбол: adapter, rules, presentation, data_provider
├── integration/             # Интеграции
│   └── llm_gateway/         # ProductionLLMGateway (Groq+Gemini+Leviathan)
├── delivery/                # Каналы доставки
│   ├── bot/                 # Telegram Bot
│   └── miniapp/             # FastAPI + API
├── tests/                   # 5 тестовых файлов (contracts, sprint2, sprint3, pipeline)
├── main.py                  # CLI entry point
└── main_prod.py             # Production entry point
```

### 2.3 Pelleto AI
- **Расположение:** `/opt/pelleto-ai/`
- **Порт:** 8131 (FastAPI)
- **Python файлов:** 37
- **Knowledge Base:** 34 факта
- **Проблема:** MCP модуль не загружается (отсутствует `mcp.server.fastmcp`)
- **Статус:** 🟡 Частично работает (API доступно, MCP нет)

### 2.4 den4ik-claude Bot
- **Расположение:** `/opt/den4ik-claude/`
- **Размер:** bot.py — 125KB (один монолитный файл!)
- **Функции:** Telegram бот, Pyrogram сессия, IPC очередь
- **Статус:** 🟢 Production

### 2.5 ArbitrCockpit
- **Расположение:** `/opt/arbitr_cockpit/`
- **Порт:** 8090
- **Технологии:** FastAPI + SQLAlchemy + Alembic + Blueprints
- **Статус:** 🟢 Production (есть ошибка DB: `no such column: platform`)

### 2.6 free-claude-code
- **Расположение:** `/root/free-claude-code/`
- **Размер:** External проект с ARCHITECTURE.md (38KB)
- **Статус:** 🟡 Внешний проект, не интегрирован

### 2.7 Вспомогательные скрипты
- `/root/bin/` — lev_start.sh, memory.py, delegate.py, handoff.py, log.py
- `/root/Claude/` — send_report.py, analyze_channel.py, translate_atp.py
- `/root/core/` — system_state.md, lastsession.md, context.md

---

## 3. ARCHITECTURE OVERVIEW

### 3.1 Leviathan Engine — Общая архитектура

```
┌─────────────────────────────────────────────────────────────┐
│                     LEVIATHAN ENGINE                         │
│                                                              │
│  ┌─────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐ │
│  │ MCP API │  │  HTTP API │  │ Telegram │  │ Claude Adapter│ │
│  │ :8300   │  │  :8200    │  │  Bot     │  │ (core_bridge) │ │
│  └────┬────┘  └────┬─────┘  └────┬─────┘  └──────┬───────┘ │
│       │             │             │                │          │
│  ┌────┴─────────────┴─────────────┴────────────────┴───────┐ │
│  │                     Dispatcher                            │ │
│  │            (Mode: manual/safe/normal/full)                │ │
│  └────────────────────────┬─────────────────────────────────┘ │
│                            │                                   │
│  ┌────────────────────────┴─────────────────────────────────┐ │
│  │                   LeviathanAgent                          │ │
│  │              (core.py — основной цикл)                    │ │
│  └────┬───────────┬──────────┬───────────┬─────────────────┘ │
│       │           │          │           │                    │
│  ┌────┴───┐ ┌────┴────┐ ┌──┴────┐ ┌────┴────┐              │
│  │ Heads  │ │ Tools   │ │Intent │ │Pipeline │              │
│  │herald/ │ │Bridge   │ │Detect │ │execution│              │
│  │mneme/  │ │13 файлов│ │       │ │backoff/ │              │
│  │reasoner│ │         │ │       │ │idempo/  │              │
│  └────────┘ └─────────┘ └───────┘ └─────────┘              │
│                                                              │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │                    Data Layer                            │ │
│  │  ┌─────────┐ ┌──────────┐ ┌──────────┐ ┌────────────┐ │ │
│  │  │leviathan│ │context_  │ │knowledge_│ │event_bus   │ │ │
│  │  │.db      │ │memory.db │ │base.db   │ │(pub/sub)   │ │ │
│  │  └─────────┘ └──────────┘ └──────────┘ └────────────┘ │ │
│  └─────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 Triton Agent — Архитектура

```
┌────────────────────────────────────────────┐
│            TRITON AGENT                     │
│                                             │
│  ┌──────────────┐  ┌──────────────────┐    │
│  │ Telegram Bot │  │ FastAPI MiniApp  │    │
│  │  delivery/   │  │  delivery/       │    │
│  └──────┬───────┘  └───────┬──────────┘    │
│         │                  │               │
│  ┌──────┴──────────────────┴──────────────┐│
│  │         ContentOrchestrator            ││
│  │   (генерация контента + трансляции)    ││
│  └──────┬──────────────────┬─────────────┘│
│         │                  │               │
│  ┌──────┴──────┐  ┌───────┴──────────┐    │
│  │ StaticSport │  │ ProductionLLM    │    │
│  │ Registry    │  │ Gateway          │    │
│  │ (Adapter)   │  │ Groq→Gemini→Lev │    │
│  └──────┬──────┘  └─────────────────┘    │
│         │                                  │
│  ┌──────┴──────────────────┐              │
│  │  Football Adapter       │              │
│  │  DataProvider → Rules → │              │
│  │  → Presentation         │              │
│  └─────────────────────────┘              │
└────────────────────────────────────────────┘
```

---

## 4. COMPONENT ANALYSIS

### 4.1 Agent Lifecycle (FSM)

**Текущая реализация:**
- Leviathan: `DispatcherModeState` (manual/safe/normal/full) + `AgentRunner` 
- Triton: `Application` класс — сборка компонентов, нет lifecycle

**Пробелы:**
- Нет явной State Machine с PAUSE/RESUME/INTERRUPT
- Нет формальных переходов между состояниями
- Нет Agent Lifecycle в понимании LangGraph

**Рекомендация:** Внедрить `AgentState` из Манифеста 5.0.0 (INIT, PLAN, REASON, ACT, OBSERVE, REFLECT, LEARN, COMPLETE, FAILED, INTERRUPTED, PAUSED, RESUMED)

**Сложность:** 6/10 | **Время:** 2-3 дня

### 4.2 Runnable Interface

**Текущая реализация:**
- Нигде нет единого Runnable интерфейса
- LeviathanAgent.run() принимает Task
- Triton Application собирается как конструктор

**Пробелы:**
- Нет `invoke/ainvoke/stream/batch` 
- Нет композиции через `|` оператор
- Нет единого контракта для всех компонентов

**Рекомендация:** Внедрить `Runnable[T, R]` протокол для всех компонентов

**Сложность:** 4/10 | **Время:** 1-2 дня

### 4.3 Middleware Pipeline

**Текущая реализация:**
- `execution/pipeline_log.py` — логирование pipeline
- `execution/backoff_scheduler.py` — backoff при ошибках
- `execution/result_envelope.py` — структурированные ответы

**Пробелы:**
- Нет формальных before/after hooks
- Нет middleware chain
- Нет возможности добавить/удалить middleware динамически

**Рекомендация:** Внедрить MiddlewarePipeline с before_model/after_model hooks

**Сложность:** 3/10 | **Время:** 1 день

### 4.4 Callback System

**Текущая реализация:**
- `EventBus` (`event_bus.py`) — простой pub/sub с 6 событиями
- PipelineLog для логирования
- Нет 12+ callbacks как в Манифесте

**Пробелы:**
- Нет структурированных callback handlers
- Нет on_llm_start/end/error
- Нет on_tool_start/end/error

**Рекомендация:** Расширить EventBus до CallbackManager с 12+ hooks

**Сложность:** 3/10 | **Время:** 1 день

### 4.5 Typed State (Pydantic v2)

**Текущая реализация:**
- Leviathan: Pydantic BaseModel в tools (AgentTaskInput, CtxStartInput и др.)
- Triton: Pydantic модели в `core/models/` (CanonicalSportEvent, MatchDTO, ScoreSchema)
- DispatcherModeState — простой класс

**Пробелы:**
- Нет единой AgentStateSchema
- Нет state migration между версиями
- Нет формальных редукторов (reducers)

**Рекомендация:** Внедрить `AgentStateSchema` как единую Pydantic модель для состояния

**Сложность:** 4/10 | **Время:** 1-2 дня

### 4.6 Checkpointing

**Текущая реализация:** ✅ **Сильная сторона**
- `ContextKeeper` (`lev_context.py`) — SQLite checkpointing
- `ctx_checkpoint` — MCP инструмент для сохранения
- `context_generator.py` — генерация .claude/claude.md
- Track: checkpoints, sessions, actions, decisions, invariants

**Пробелы:**
- Нет multi-backend (только SQLite)
- Нет resume после прерывания

**Улучшение:** Добавить InMemory + Postgres бэкенды

**Сложность:** 5/10 | **Время:** 2-3 дня

### 4.7 Durable Execution

**Текущая реализация:** ✅ **Сильная сторона**
- `OperationRegistry` — идемпотентность (SQLite, 7d TTL)
- `BackoffScheduler` — backoff при 429 ошибках
- `ResultEnvelope` — структурированные ответы (SUCCESS/PARTIAL/ERROR)
- `Dispatcher` — queue-based execution

**Пробелы:**
- Нет формального ACK/NACK механизма
- Нет DLQ (Dead Letter Queue)

**Улучшение:** Добавить DurableEventBus с ACK/NACK и DLQ

**Сложность:** 5/10 | **Время:** 2 дня

### 4.8 Memory System

**Текущая реализация:** ⚠️
- `ContextMemory` (`db/context_memory.py`) — SQLite с авто-eviction
- `knowledge_base.py` — база знаний
- `context_ledger.py` — журнал контекста
- `heads/mneme/` — memory loader

**Пробелы:**
- Только SQLite бэкенд
- Нет short-term/long-term/episodic/semantic/working разделения
- Нет векторного поиска (semantic)

**Рекомендация:** Внедрить MemorySystem с 5 типами памяти

**Сложность:** 6/10 | **Время:** 2-3 дня

### 4.9 Plugin SDK

**Текущая реализация:** ❌
- Нет Plugin системы
- Нет install/load/enable/disable/uninstall lifecycle
- Нет PluginAPI

**Рекомендация:** Внедрить PluginSDK по образцу OpenHands

**Сложность:** 7/10 | **Время:** 3-4 дня

### 4.10 Workflow Engine

**Текущая реализация:** ❌
- Нет Workflow Engine
- Нет Branch/Merge/Parallel
- Нет Saga pattern

**Рекомендация:** Внедрить WorkflowEngine по образцу CrewAI Flows

**Сложность:** 8/10 | **Время:** 3-5 дней

### 4.11 Scheduler

**Текущая реализация:** ⚠️
- Triton: `core/scheduler.py` — фоновый планировщик опроса данных
- Leviathan: Нет встроенного планировщика

**Рекомендация:** Внедрить Scheduler с Cron/Delayed/Recurring

**Сложность:** 5/10 | **Время:** 2 дня

### 4.12 Model Router

**Текущая реализация:** ⚠️
- Leviathan: `agent/model_router.py` + `agent/providers.py` + Gemini keys pool
- Triton: `ProductionLLMGateway` с Circuit Breaker (Groq → Gemini → Leviathan)
- Нет cost-aware routing

**Рекомендация:** Внедрить ModelRouter с cost/latency/fallback политиками

**Сложность:** 5/10 | **Время:** 2 дня

### 4.13 Observability

**Текущая реализация:** ⚠️
- `execution/pipeline_log.py` — PipelineEvent логгирование
- `db/journal.py` — ExecutionJournal
- `db/token_stats.py` — статистика токенов
- `db/context_ledger.py` — ContextLedger

**Пробелы:**
- Нет OTEL интеграции
- Нет cost tracking
- Нет метрик latency/tokens

**Рекомендация:** Внедрить OTEL GenAI semantic conventions

**Сложность:** 6/10 | **Время:** 2-3 дня

### 4.14 Security

**Текущая реализация:** ⚠️
- `agent/tools_security.py` — PII detection, dangerous patterns
- `execution/result_envelope.py` — PolicyEngine проверка команд
- DANGEROUS_PATTERNS в tools.py

**Пробелы:**
- Нет formal Firewall
- Нет RBAC
- Нет Prompt Injection Protection

**Рекомендация:** Внедрить NFKC + Two-Layer Firewall + RBAC

**Сложность:** 6/10 | **Время:** 2-3 дня

### 4.15 Multi-Agent Coordination

**Текущая реализация:** ❌
- Нет Coordinator
- Нет Arbiter
- Нет consensus/voting
- Нет GroupChat

**Рекомендация:** Внедрить Coordinator+Arbiter по образцу AutoGen

**Сложность:** 8/10 | **Время:** 4-5 дней

---

## 5. GAPS & RECOMMENDATIONS

### 5.1 Критические проблемы (CRITICAL)

| # | Проблема | Проект | Решение | Сложность |
|---|----------|--------|---------|-----------|
| 1 | **Нет Workflow Engine** | Leviathan | Внедрить Branch/Merge/Parallel/Saga | 8/10 |
| 2 | **Нет Multi-Agent** | Leviathan | Coordinator + Arbiter | 8/10 |
| 3 | **Нет Sandbox** | Оба | Внедрить DockerSandbox | 7/10 |
| 4 | **Нет Plugin SDK** | Leviathan | install/load/enable/disable/uninstall | 7/10 |

### 5.2 Высокоприоритетные (HIGH)

| # | Проблема | Проект | Решение | Сложность |
|---|----------|--------|---------|-----------|
| 5 | **Нет Agent Lifecycle (FSM)** | Leviathan | AgentState с PAUSE/RESUME/INTERRUPT | 6/10 |
| 6 | **Нет Runnable Interface** | Оба | invoke/stream/batch для всех компонентов | 4/10 |
| 7 | **Memory System v1** | Leviathan | 5 типов памяти (short/long/episodic/semantic/working) | 6/10 |
| 8 | **Нет Middleware Pipeline** | Leviathan | before_model/after_model hooks | 3/10 |
| 9 | **Нет Callback System** | Оба | 12+ callback hooks | 3/10 |

### 5.3 Средний приоритет (MEDIUM)

| # | Проблема | Проект | Решение | Сложность |
|---|----------|--------|---------|-----------|
| 10 | **Нет OTEL Observability** | Оба | OTEL GenAI semantic conventions | 6/10 |
| 11 | **Security v1** | Leviathan | NFKC + Firewall + Prompt Injection | 6/10 |
| 12 | **Нет Scheduler** | Leviathan | Cron/Delayed/Recurring | 5/10 |
| 13 | **Model Router v1** | Оба | cost-aware routing | 5/10 |
| 14 | **MCP Pelleto не работает** | Pelleto | Установить fastmcp | 2/10 |

### 5.4 Проблемы качества кода

| # | Проблема | Проект | Описание |
|---|----------|--------|----------|
| 1 | **Огромный bot.py** (125KB) | den4ik-claude | Один файл с тысячами строк — критично для поддержки |
| 2 | **Дублирование кода** | Leviathan | .bak файлы рядом с оригиналами |
| 3 | **Нет тестов** | Leviathan | 121 Python файл, 0 тестов |
| 4 | **Нет .env.example** | Triton | Только .env с живыми ключами |
| 5 | **Бэкапы в репозитории** | Pelleto | fix_body.py, fix_paths.py — не нужны в production |

---

## 6. CODE EXAMPLES — УЛУЧШЕНИЯ

### 6.1 AgentState (FSM) — интеграция в Leviathan

```python
# /opt/leviathan_engine/agent_service/agent/state.py
from enum import Enum
from dataclasses import dataclass, field
from datetime import datetime
from typing import Optional, Dict, Any, List
import asyncio

class AgentState(Enum):
    INIT = "init"
    PLAN = "plan"
    REASON = "reason"
    ACT = "act"
    OBSERVE = "observe"
    REFLECT = "reflect"
    LEARN = "learn"
    COMPLETE = "complete"
    FAILED = "failed"
    INTERRUPTED = "interrupted"
    PAUSED = "paused"
    RESUMED = "resumed"

@dataclass
class StateTransition:
    from_state: AgentState
    to_state: AgentState
    timestamp: datetime = field(default_factory=datetime.utcnow)
    metadata: Dict[str, Any] = field(default_factory=dict)

class StateMachine:
    VALID_TRANSITIONS = {
        AgentState.INIT: [AgentState.PLAN, AgentState.FAILED],
        AgentState.PLAN: [AgentState.REASON, AgentState.INTERRUPTED, AgentState.FAILED],
        AgentState.REASON: [AgentState.ACT, AgentState.REFLECT, AgentState.FAILED],
        AgentState.ACT: [AgentState.OBSERVE, AgentState.INTERRUPTED, AgentState.FAILED],
        AgentState.OBSERVE: [AgentState.REFLECT, AgentState.REASON, AgentState.COMPLETE, AgentState.FAILED],
        AgentState.REFLECT: [AgentState.LEARN, AgentState.REASON, AgentState.FAILED],
        AgentState.COMPLETE: [],
        AgentState.FAILED: [AgentState.INIT],
        AgentState.INTERRUPTED: [AgentState.RESUMED, AgentState.FAILED],
        AgentState.PAUSED: [AgentState.RESUMED, AgentState.FAILED],
        AgentState.RESUMED: [AgentState.REASON, AgentState.PLAN],
    }

    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        self.current_state = AgentState.INIT
        self.history: List[StateTransition] = []
        self._lock = asyncio.Lock()

    async def transition(self, to_state: AgentState, metadata: Dict[str, Any] = None) -> bool:
        async with self._lock:
            if to_state not in self.VALID_TRANSITIONS.get(self.current_state, []):
                raise ValueError(f"Invalid transition: {self.current_state} -> {to_state}")
            transition = StateTransition(
                from_state=self.current_state,
                to_state=to_state,
                metadata=metadata or {}
            )
            self.history.append(transition)
            self.current_state = to_state
            return True

    def can_transition(self, to_state: AgentState) -> bool:
        return to_state in self.VALID_TRANSITIONS.get(self.current_state, [])
```

### 6.2 Runnable Interface — интеграция

```python
# /opt/leviathan_engine/agent_service/agent/runnable.py
from abc import ABC, abstractmethod
from typing import TypeVar, Generic, AsyncIterator, List, Dict, Any

T = TypeVar('T')
R = TypeVar('R')

class Runnable(Generic[T, R], ABC):
    @abstractmethod
    async def invoke(self, input: T, config: Dict[str, Any] = None) -> R: ...
    @abstractmethod
    async def ainvoke(self, input: T, config: Dict[str, Any] = None) -> R: ...
    @abstractmethod
    async def stream(self, input: T, config: Dict[str, Any] = None) -> AsyncIterator[R]: ...
    @abstractmethod
    async def batch(self, inputs: List[T], config: Dict[str, Any] = None) -> List[R]: ...

    def __or__(self, other: 'Runnable[R, Any]') -> 'RunnableSequence':
        return RunnableSequence([self, other])
```

### 6.3 MemorySystem — 5 типов памяти

```python
# /opt/leviathan_engine/agent_service/db/memory_system.py
class ShortTermMemory:
    """Кратковременная память (TTL: 30 мин, max: 100)"""
    ...

class LongTermMemory:
    """Долговременная память (semantic search)"""
    ...

class EpisodicMemory:
    """Эпизодическая память (опыт, события)"""
    ...

class SemanticMemory:
    """Семантическая память (факты, триплеты)"""
    ...

class WorkingMemory:
    """Рабочая память (текущий контекст, max: 20)"""
    ...
```

### 6.4 DurableEventBusV2 с DLQ

```python
# /opt/leviathan_engine/agent_service/event_bus_v2.py
from dataclasses import dataclass
from datetime import datetime, timedelta
import asyncio
from typing import Callable, Optional, Dict, Any, List

@dataclass
class DurableEvent:
    event_id: str
    event_type: str
    payload: Dict[str, Any]
    idempotency_key: str
    priority: int = 0
    max_retries: int = 3
    created_at: datetime = field(default_factory=datetime.utcnow)

class DurableEventBusV2:
    def __init__(self, worker_count: int = 3):
        self._queue: asyncio.PriorityQueue = asyncio.PriorityQueue()
        self._dlq: asyncio.Queue = asyncio.Queue()
        self._handlers: Dict[str, List[Callable]] = {}
        self._processed: Dict[str, datetime] = {}  # idempotency cache

    def subscribe(self, event_type: str, handler: Callable) -> None: ...
    async def publish(self, event: DurableEvent) -> bool: ...
    async def start(self) -> None: ...
    async def stop(self) -> None: ...
    def get_metrics(self) -> Dict[str, int]: ...
```

---

## 7. MIGRATION PLAN — 5.0.0

### Фаза 1: Быстрые победы (1-2 дня)
1. 🔧 **Починить MCP Pelleto** — установить `mcp` pip пакет
2. 📦 **Разделить bot.py** на модули
3. 🧪 **Добавить тесты Leviathan** (хотя бы smoke tests)
4. 🧹 **Очистить .bak файлы и мусор**

### Фаза 2: Core improvements (3-5 дней)
5. 🏛️ **Внедрить AgentState FSM** в LeviathanAgent
6. 🔄 **Внедрить Runnable Interface** для всех компонентов
7. ⛓️ **Добавить Middleware Pipeline**
8. 🔔 **Расширить EventBus до CallbackManager**

### Фаза 3: Advanced features (5-8 дней)
9. 🧠 **MemorySystem V2** — 5 типов памяти
10. 🔧 **PluginSDK** — install/load/enable/disbale/uninstall
11. 🔀 **WorkflowEngine** — Branch/Merge/Parallel/Saga
12. ⏰ **Scheduler** — Cron/Delayed/Recurring

### Фаза 4: Enterprise (5-7 дней)
13. 🔒 **Security V3** — Firewall + PII + RBAC + Prompt Injection
14. 📊 **Observability V2** — OTEL GenAI + Cost Tracking
15. 🏗️ **Multi-Agent** — Coordinator + Arbiter
16. 🐳 **Sandbox** — Docker isolation

### Фаза 5: Production hardening (3-5 дней)
17. 🔬 **Тестирование** — unit + integration + e2e
18. 📈 **Performance** — профилирование, оптимизация
19. 📚 **Документация** — ARCHITECTURE.md, ADRs

**Общее время:** 17-27 дней  
**Приоритет:** Фаза 1 → Фаза 2 → Фаза 3 → Фаза 4 → Фаза 5

---

## 8. COMPARISON TABLE — Манифест 5.0.0

| № | Компонент | Манифест 5.0.0 | Leviathan | Triton | Статус |
|---|-----------|----------------|-----------|--------|--------|
| 1 | Agent Lifecycle | ✅ AgentState FSM (12 состояний) | ⚠️ DispatcherModeState (4 режима) | ❌ Application class | ⚠️ |
| 2 | Runnable Interface | ✅ invoke/stream/batch | ❌ Нет | ❌ Нет | ❌ |
| 3 | Middleware Pipeline | ✅ 6 hooks (before/after) | ⚠️ PipelineLog + Backoff | ❌ Нет | ⚠️ |
| 4 | Callback System | ✅ 12+ callback hooks | ⚠️ EventBus (6 событий) | ❌ Нет | ⚠️ |
| 5 | Typed State | ✅ Pydantic v2 + migration | ⚠️ Pydantic в tools | ✅ Pydantic модели | ⚠️ |
| 6 | Checkpointing | ✅ Multi-backend | ✅ ContextKeeper + SQLite | ❌ Нет | ✅ |
| 7 | Durable Execution | ✅ ACK/NACK/Idempotency | ✅ OperationRegistry + Backoff | ⚠️ Circuit Breaker | ✅ |
| 8 | Memory System | ✅ 5 типов памяти | ⚠️ ContextMemory (SQLite) | ❌ Нет | ⚠️ |
| 9 | Plugin SDK | ✅ Full lifecycle | ❌ Нет | ❌ Нет | ❌ |
| 10 | Capability Discovery | ✅ CapabilityRegistry | ⚠️ Intent Detection | ❌ Нет | ⚠️ |
| 11 | Workflow Engine | ✅ Branch/Merge/Saga | ❌ Нет | ❌ Только pipeline | ❌ |
| 12 | Scheduler | ✅ Cron/Delayed/Recurring | ❌ Нет | ✅ core/scheduler.py | ⚠️ |
| 13 | Observability (OTEL) | ✅ OTEL GenAI | ⚠️ PipelineLog | ❌ Нет | ⚠️ |
| 14 | Sandbox System | ✅ Docker Isolation | ❌ Нет | ❌ Нет | ❌ |
| 15 | Model Router | ✅ Cost/Latency/Fallback | ⚠️ Basic providers | ✅ Circuit Breaker Router | ⚠️ |
| 16 | Security | ✅ NFKC+Firewall+PII+RBAC | ⚠️ PolicyEngine + PII | ❌ Нет | ⚠️ |
| 17 | Multi-Agent | ✅ Coordinator+Arbiter | ❌ Нет | ❌ Нет | ❌ |
| 18 | Self-Reflection | ✅ Critic+Evaluator | ⚠️ Reasoner/Evaluator | ❌ Нет | ⚠️ |
| 19 | Governance | ✅ Audit+Compliance+Risk | ⚠️ ContextLedger + journal | ❌ Нет | ⚠️ |
| 20 | Enterprise HA | ✅ HA/Scaling/DR | ❌ Нет | ❌ Нет | ❌ |

**ИТОГО:** ✅ 2/20 + ⚠️ 11/20 + ❌ 7/20 = **9.5/20**

---

## 9. STRONG SIDES — ЧТО УЖЕ ХОРОШО

### Leviathan Engine ✅
1. **ContextKeeper** — отличная система управления контекстом с SQLite
2. **OperationRegistry** — production-ready идемпотентность
3. **BackoffScheduler** — устойчивость к rate limiting
4. **ResultEnvelope** — структурированные ответы
5. **EventBus** — простой, но рабочий pub/sub
6. **Intent Detection** — определение намерений
7. **MCP сервер** — 19 инструментов для доступа к серверу
8. **13 tools модулей** — богатая экосистема инструментов

### Triton Agent ✅
1. **Чистая архитектура** — разделение на слои (core/sports/delivery/integration)
2. **ABI интерфейсы** — SportDataProvider, SportRules, SportPresentation
3. **ProductionLLMGateway** — Circuit Breaker + multi-provider
4. **Pydantic модели** — CanonicalSportEvent, MatchDTO, ScoreSchema
5. **Тесты** — 5 тестовых файлов с покрытием models, registry, pipeline

---

## 10. WEAK SIDES — ЧТО НУЖНО УЛУЧШИТЬ

### Leviathan Engine ❌
1. **Нет тестов** — 121 Python файл, 0 тестов
2. **Нет структурированного состояния** — AgentStateSchema отсутствует
3. **Большой main.py** (1036 строк) — слишком много логики в одном файле
4. **Дублирование .bak файлов** — 1.5MB мусора
5. **Нет Docker** — всё работает на голом сервере
6. **Смешение responsibilities** — agent/tg_bot.py содержит и бота, и раннер
7. **Мало аннотаций типов** — многие функции без type hints
8. **Нет единого архитектурного стиля** — core.py, dispatcher.py, event_bus.py в корне

### Triton Agent ❌
1. **Нет checkpointing** — при перезапуске всё теряется
2. **Нет durable execution** — нет retry/DLQ для задач
3. **Нет memory system** — нет долговременной памяти
4. **Малый набор тестов** — только contracts + unit

### Pelleto AI ❌
1. **MCP не работает** — отсутствует fastmcp установка
2. **Мусорные файлы** — fix_body.py, fix_js.py, fix_logs.py

---

## 11. ЗАКЛЮЧЕНИЕ

**Leviathan Engine** — мощная, но сырая система. Есть отличные компоненты (ContextKeeper, OperationRegistry), но отсутствует архитектурная целостность. Переход к Манифесту 5.0.0 даст системе формальный lifecycle, middleware pipeline и workflow engine.

**Triton Agent** — пример чистой архитектуры с разделением слоёв, но не хватает enterprise-фич (checkpointing, durable execution).

**Pelleto AI** — требует минимальных доработок (установка fastmcp).

**Рекомендация:** Начать с Фазы 1 (быстрые победы), затем Фаза 2 (core improvements).
Ключевой приоритет — тесты и Agent Lifecycle.

---

**Создано:** 2026-07-01 07:52 UTC  
**Автор:** LEVIATHAN ANALYSIS AGENT  
**Файл:** `/root/analysis/MY_SYSTEM_ANALYSIS.md`
