# 🔀 ПЛАН ОБЪЕДИНЕНИЯ СИСТЕМ: ADR-002 КАК ЯДРО
## Leviathan Engine + Triton + den4ik-claude + ArbitrCockpit + Pelleto → Единая платформа

**Дата:** 2026-07-02 | **Сервер:** LEVIATHAN (78.17.24.96)
**Статус:** DRAFT | **Основание:** ADR-002-FINAL (Dynamic Scenario-Driven Agent Execution Engine)
**Версия:** 1.0

---

## 1. ЗАЧЕМ ЭТО НУЖНО

### Проблема
На сервере работают **5+ независимых агентных систем**, каждая со своей архитектурой:

| Система | Строк кода | Процесс | Порт | LLM-вызовы |
|---------|-----------|---------|------|------------|
| Leviathan Engine | ~25,000 | leviathan_agent + leviathan_mcp | :8200, :8300 | Gemini/Claude/Groq |
| Triton Agent | ~3,900 | sport-observer | :8201 | Groq→Gemini→Leviathan |
| den4ik-claude | ~125,000 (монолит) | den4ik-claude | TG only | Встроен |
| ArbitrCockpit | ~8,000 | arbitr | :8090 | FastAPI + SQLAlchemy |
| Pelleto AI | ~5,000 | pelleto | :8131 | Встроен |
| **ИТОГО** | **~167,000** | **5+ процессов** | **~1.5 GiB RAM** | **Каждый свой** |

### Цель
Перевести всё это на **централизованное ядро ADR-002**: runtime decision-making kernel, где каждый агент — это просто `ScenarioPolicy` (YAML-контракт) + `BaseAgent` (Python-реализация).

---

## 2. 🔬 ЧТО ТАКОЕ ADR-002 (КРАТКО)

ADR-002 — это архитектурное решение, заменяющее **статический pipeline** на **runtime decision-making kernel**.

### Было (pipeline):
```
Task → Plan → Step 1 → Step 2 → ... → Step N → Result
```

### Стало (runtime kernel):
```
ScenarioPolicy (YAML) → AgentKernel
  ├── 1. Decision Engine — что делать дальше?
  ├── 2. Role Selector — кто будет делать?
  ├── 3. Execution Loop — сделать → оценить
  ├── 4. Replanning Engine — перепланировать при неудаче
  └── 5. Safety Guards — защита от зацикливания/перерасхода
```

### Ключевые компоненты ADR-002:

| Компонент | Назначение | Сложность | Готовность |
|-----------|-----------|-----------|------------|
| `ScenarioPolicy` | YAML-контракт: intent, roles, constraints, safety | 4/10 | 📋 План |
| `DecisionEngine` | Выбор следующего шага (rules + LLM + cost) | 8/10 | 📋 План |
| `RoleSelector` | Динамический выбор роли на каждом шаге | 5/10 | 📋 План |
| `ExecutionLoop` | observe → decide → act → evaluate | 8/10 | 📋 План |
| `ReplanningEngine` | Адаптивное перепланирование | 7/10 | 📋 План |
| `SafetyGuards` | max_steps, max_cost, timeout, circuit_breaker | 6/10 | 📋 План |

---

## 3. 🗺️ КАРТА МЕРДЖА: ЧТО И КУДА ПЕРЕЕЗЖАЕТ

### 3.1 Схема целевой архитектуры

```
┌─────────────────────────────────────────────────────────────────────┐
│                     CLI / API / MCP / TG BOT                        │
│              (Единый вход: lev-cli, :8200, :8300, @bot)            │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────────┐
│                    ADR-002 AGENT KERNEL                             │
│                                                                      │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  Decision Engine    Role Selector    Execution Loop           │  │
│  │  Replanning Engine  Safety Guards    Cost Manager             │  │
│  └──────────────────────────────────────────────────────────────┘  │
│                           │                                         │
│  ┌──────────────────────────────────────────────────────────────┐  │
│  │  ScenarioPolicy Loader (YAML → Pydantic)                     │  │
│  │  - freelance_seo.yaml   - sport_observer.yaml                │  │
│  │  - code_project.yaml    - tg_bot_handler.yaml                │  │
│  └──────────────────────────────────────────────────────────────┘  │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────────┐
│                    SHARED INFRASTRUCTURE                            │
│                                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────────┐  │
│  │ LLM Gateway  │  │ Memory V2    │  │ Secrets Vault            │  │
│  │ unified      │  │ 5 типов      │  │ AES-256, :8400           │  │
│  ├──────────────┤  ├──────────────┤  ├──────────────────────────┤  │
│  │ EventBus     │  │ Observ.      │  │ Checkpoint/Scheduler     │  │
│  │ durable+DLQ  │  │ OTEL+Cost    │  │ ContextKeeper            │  │
│  └──────────────┘  └──────────────┘  └──────────────────────────┘  │
└──────────────────────────┬──────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────────┐
│                    ROLE / TOOL REGISTRY                             │
│  (все роли и инструменты со всех существующих агентов)              │
│                                                                      │
│  ┌────────────────┐  ┌────────────────┐  ┌──────────────────────┐  │
│  │ Из Leviathan:  │  │ Из Triton:     │  │ Из den4ik-claude:    │  │
│  │ 13 tools       │  │ FootballMatch  │  │ TG message send      │  │
│  │ Intent Detect  │  │ SportData      │  │ Media upload         │  │
│  │ Pipeline exec  │  │ Notifications  │  │ Account management   │  │
│  ├────────────────┤  ├────────────────┤  ├──────────────────────┤  │
│  │ Из Arbitr:     │  │ Из Pelleto:    │  │ Из Manifest 5.1:     │  │
│  │ Case status    │  │ KnowledgeBase  │  │ StateMachine (FSM)   │  │
│  │ Document mgmt  │  │ Skill loader   │  │ WorkflowEngine       │  │
│  └────────────────┘  └────────────────┘  └──────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.2 Маппинг существующего кода на ADR-002

| Существующий модуль | Проект | Куда в ADR-002 | Статус |
|--------------------|--------|----------------|--------|
| `agent/core.py` (LeviathanAgent) | Leviathan | → `Role` (GeneralPurpose) / `Tool` (Bridge) | 🔄 Обёртка |
| `agent/tools*.py` (13 файлов, 3K строк) | Leviathan | → `ToolRegistry` | 🔄 Регистрация |
| `agent/intent.py` | Leviathan | → `DecisionEngine` (Rule Filter) | 🔄 Интеграция |
| `agent/dispatcher.py` | Leviathan | → `ExecutionLoop` | 🔄 Замена |
| `agent/providers.py` + `model_router.py` | Leviathan | → `LLM Gateway` | 🔄 Слияние |
| `agent/tg_bot.py` | Leviathan | → `Interface Layer` (Telegram) | 🔄 Перенос |
| `mcp_server/` (19 tools) | Leviathan | → `Interface Layer` (MCP) | 🔄 Перенос |
| `middleware/pipeline.py` | Leviathan | → `SafetyGuards` | 🔄 Интеграция |
| `callbacks/manager.py` | Leviathan | → `AgentKernel` (Callbacks) | 🔄 Интеграция |
| `workflow/engine.py` | Leviathan | → `ExecutionLoop` (Branch/Merge) | 🔄 Интеграция |
| `db/memory_system.py` | Leviathan | → `Memory System` | 🔄 Shared Service |
| `event_bus.py` | Leviathan | → `EventBus` (Durable) | 🔄 Расширение |
| `execution/` (backoff, idempotency) | Leviathan | → `SafetyGuards` | 🔄 Интеграция |
| Triton: `ContentOrchestrator` | Triton | → `Role` (SportObserver) | 🔄 Обёртка |
| Triton: `ProductionLLMGateway` | Triton | → `LLM Gateway` (CircuitBreaker) | 🔄 Слияние |
| Triton: `FootballAdapter` | Triton | → `Tool` (SportDataProvider) | 🔄 Регистрация |
| den4ik-claude: `bot.py` | den4ik | → `Role` (TelegramHandler) | 🔄 Модуляризация |
| ArbitrCockpit: API | Arbitr | → `Role` (ArbitrAnalyst) | 🔄 Обёртка |
| Pelleto: KnowledgeBase | Pelleto | → `Memory System` (LongTerm) | 🔄 Интеграция |

---

## 4. 📋 ПОЭТАПНЫЙ ПЛАН МЕРДЖА

### 4.1 Сводка фаз

| Фаза | Дней | Фокус | Результат |
|------|------|-------|-----------|
| **Фаза 1: Ядро** | 3 | ScenarioPolicy + DecisionEngine + ExecutionLoop | Базовый kernel работает |
| **Фаза 2: Роли и безопасность** | 4 | RoleRegistry + SafetyGuards + Checkpointing | Kernel защищён |
| **Фаза 3: Слияние агентов** | 5 | Обёртка всех существующих агентов в роли | Все системы под единым ядром |
| **Фаза 4: CLI и интерфейсы** | 2 | lev-cli + API + MCP + TG | Единые точки входа |
| **Фаза 5: Production** | 3 | Тесты, доки, performance | Production-ready |

**Итого:** ~17 дней

---

### 4.2 Фаза 1 — ЯДРО ADR-002 (3 дня)

**Создаётся** в `/opt/leviathan_engine/agent_service/`:

```
core/                               # 🆕 Новая директория
├── scenarios/
│   ├── __init__.py
│   ├── loader.py                  # ScenarioPolicy (Pydantic) — YAML → модель
│   └── examples/
│       └── freelance_seo.yaml     # Пример сценария из ADR-002
├── agent/
│   ├── __init__.py
│   ├── kernel.py                  # AgentKernel — главный оркестратор
│   ├── decision_engine.py         # DecisionEngine (Rule Filter + LLM Ranking)
│   ├── execution_loop.py          # ExecutionLoop (observe→decide→act→eval)
│   └── replanner.py               # ReplanningEngine
├── roles/
│   ├── __init__.py
│   ├── registry.py                # RoleRegistry — регистр ролей
│   └── base.py                    # BaseRole(Runnable) — базовый интерфейс
└── safety/
    ├── __init__.py
    └── guards.py                  # SafetyGuards (max_steps, cost, timeout)
```

**Как мержится существующий код:**
- `agent/intent.py` → читается как inspiration для `DecisionEngine`
- `agent/state.py` (StateMachine) → встраивается в `ExecutionLoop` как внутреннее состояние
- `middleware/pipeline.py` → становится частью `SafetyGuards`
- `workflow/engine.py` → встраивается в `ExecutionLoop` как Branch/Merge

**Критерий:** `ScenarioPolicy` загружается из YAML, `DecisionEngine` выбирает роль, `ExecutionLoop` делает 1 шаг.

---

### 4.3 Фаза 2 — РОЛИ И БЕЗОПАСНОСТЬ (4 дня)

**Создаётся/расширяется:**

```
core/
├── roles/
│   └── registry.py                # RoleRegistry с fuzzy match
├── safety/
│   ├── guards.py                  # Расширение: все 7 механизмов ADR-002
│   ├── cost_manager.py            # Budget enforcement
│   ├── loop_detector.py           # Детектор зацикливания
│   └── circuit_breaker.py         # Circuit breaker (из Triton)
├── artifacts/
│   └── manager.py                 # ArtifactManager (state.json, reasoning_trace.jsonl)
system/
├── event_bus_durable.py           # DurableEventBus (ACK/NACK/DLQ) — расширение event_bus.py
├── checkpoint_manager.py          # Checkpointing (из lev_context.py)
└── observability.py               # OTEL + Cost Tracking
```

**Как мержится существующий код:**
- Triton `ProductionLLMGateway` → circuit breaker выносится в `core/safety/`
- Leviathan `execution/backoff_scheduler.py` → интегрируется в `SafetyGuards`
- Leviathan `execution/idempotency.py` → интегрируется в `EventBus`
- Контекст: `mcp_server/lev_context.py` (ContextKeeper) → `checkpoint_manager.py`

**Критерий:** Kernel не даёт превысить max_cost, детектирует loop, сохраняет checkpoint после каждого шага.

---

### 4.4 Фаза 3 — СЛИЯНИЕ АГЕНТОВ (5 дней)

**Каждый существующий агент оборачивается в `BaseRole`:**

```
core/roles/
├── general_role.py             # LeviathanAgent → GeneralPurposeRole
├── sport_observer_role.py      # Triton → SportObserverRole
├── arbitr_role.py              # ArbitrCockpit → ArbitrRole
├── tg_handler_role.py          # den4ik-claude → TelegramHandlerRole
└── pelleto_role.py             # Pelleto → PelletoRole
```

**Процесс слияния для каждого агента:**

#### 3.1 GeneralRole (из Leviathan Agent, 1 день)
```
agent/core.py → core/roles/general_role.py
  └── LeviathanAgent.run() → GeneralRole.execute()
  └── Сохраняется как pipeline mode (legacy) + runtime mode (new)
```

#### 3.2 SportObserverRole (из Triton, 1 день)
```
/opt/triton_agent/ → core/roles/sport_observer_role.py
  └── ContentOrchestrator → сценарий sport_observer.yaml
  └── FootballAdapter → ToolRegistry
  └── ProductionLLMGateway → CircuitBreaker в shared
  └── /opt/triton_agent/ остаётся как есть (symlink на core/roles/)
```

#### 3.3 TelegramHandlerRole (из den4ik-claude, 1.5 дня)
```
/opt/den4ik-claude/bot.py → core/roles/tg_handler_role.py
  └── Разделение: message_handler, command_handler, session_manager
  └── 125KB монолит → 3-4 модуля в core/roles/tg_handler/
  └── Исходный bot.py остаётся для отката
```

#### 3.4 ArbitrRole (из ArbitrCockpit, 0.5 дня)
```
/opt/arbitr_cockpit/ → core/roles/arbitr_role.py
  └── FastAPI эндпоинты → ScenarioPolicy arbitr_case.yaml
  └── Обёртка без слома существующего API
```

#### 3.5 PelletoRole (из Pelleto, 1 день)
```
/opt/pelleto-ai/ → core/roles/pelleto_role.py
  └── Починить MCP (установить fastmcp)
  └── KnowledgeBase → memory system
```

**Критерий:** `AgentKernel` может запустить любой из 5 сценариев через единый интерфейс.

---

### 4.5 Фаза 4 — CLI И ИНТЕРФЕЙСЫ (2 дня)

**Единая точка входа `lev-cli`:**

```
cli/
├── commands.py              # Все команды: run, list, show, watch, cancel, resume
└── scenarios/               # YAML-сценарии
    ├── freelance_seo.yaml
    ├── sport_observer.yaml
    ├── arbit_case.yaml
    └── tg_bot_handler.yaml
```

**Команды из ADR-002:**
```bash
# Запуск любого сценария
lev-cli run scenario sport_observer --mode PRO --budget 5.0
lev-cli run scenario freelance_seo --mode FLASH --provider deepseek
lev-cli run scenario arbit_case --mode AUTO

# Управление
lev-cli scenarios list
lev-cli run watch <execution_id>
lev-cli run cancel <execution_id>
lev-cli run resume <execution_id>
```

**Интерфейсы (существующие не ломаются):**
- CLI: `lev-cli` (новый)
- HTTP: `:8200` (старый) + новый OpenAI-compatible API
- MCP: `:8300` (старый) + все инструменты всех агентов
- Telegram: существующий бот + роутинг в ядро

**Критерий:** Одна команда запускает агента любой системы.

---

### 4.6 Фаза 5 — PRODUCTION (3 дня)

| Шаг | Действие |
|-----|----------|
| 5.1 | Unit-тесты: DecisionEngine, ExecutionLoop, SafetyGuards (≥80%) |
| 5.2 | Integration-тесты: Full Loop, Replanning, Safety Violation, Resume |
| 5.3 | Performance-тесты: decision latency <2s, checkpoint <100ms |
| 5.4 | Документация: ARCHITECTURE.md, SCENARIO_SPEC.md, CLI_GUIDE.md |
| 5.5 | Примеры сценариев: freelance_seo, sport_observer |

---

## 5. 🔄 ОБРАТНАЯ СОВМЕСТИМОСТЬ

**Ключевой принцип:** ни один существующий сервис не ломается.

```python
# LeviathanAgent переключается между режимами:
class LeviathanAgent:
    def __init__(self, mode: str = "pipeline"):
        self.mode = mode
        if mode == "runtime":
            self.kernel = AgentKernel(...)
        else:
            self.pipeline = PipelineExecutor(...)
    
    async def run(self, task):
        if self.mode == "runtime":
            return await self.kernel.execute(task)
        else:
            return await self.pipeline.execute(task)
```

**Режим по умолчанию:** `pipeline` (всё работает как сейчас)
**Переключение:** `lev-cli config set execution_mode runtime`

---

## 6. ⏱️ ТАЙМЛАЙН

```
Фаза 1 (3 дня): ███████░░░░░░░░░░░░░░░░░░░░░░░
Фаза 2 (4 дня): ███████████████░░░░░░░░░░░░░░░
Фаза 3 (5 дней): █████████████████████████░░░░░
Фаза 4 (2 дня): █████████████████████████████░
Фаза 5 (3 дня): ██████████████████████████████
                ───────────────────────────────
                 День 1                   День 17
```

---

## 7. 🎯 ЧТО ДАЁТ МЕРДЖ

| Метрика | Сейчас | После |
|---------|--------|-------|
| **Процессов** | 5+ | 2 (ядро + worker pool) |
| **RAM** | ~1.5 GiB | ~500 MiB |
| **LLM Gateway** | 4 реализации | 1 (unified) |
| **Очередная команда** | выбрать систему → зайти в её контекст | `lev-cli run scenario X` |
| **Новый сценарий** | писать pipeline-код днями | написать YAML + роль за часы |
| **Cost tracking** | нет | built-in (max_cost_usd) |

---

## 8. ПЕРВЫЙ ШАГ (что делать прямо сейчас)

```bash
# 1. Создать структуру ядра ADR-002
mkdir -p /opt/leviathan_engine/agent_service/core/{scenarios,agent,roles,safety,artifacts}

# 2. Создать первый файл
touch core/__init__.py
touch core/scenarios/__init__.py
touch core/scenarios/loader.py      # ScenarioPolicy (Pydantic)
```

---

*Документ создан: 2026-07-02 | Основание: ADR-002-FINAL*
