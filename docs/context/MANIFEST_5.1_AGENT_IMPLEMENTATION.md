# ЗАДАЧА: ПОЛНАЯ РЕАЛИЗАЦИЯ МАНИФЕСТА 5.1.0 — АГЕНТНОЕ ЯДРО

## ⚠️ КРИТИЧЕСКИЕ ТРЕБОВАНИЯ
- Работай АВТОНОМНО без остановок и подтверждений
- Логируй КАЖДОЕ действие в /root/implementation_log.md
- Создавай отчёты после каждого этапа в /root/analysis/
- При ошибках — логируй и продолжай со следующего шага
- НЕ спрашивай "продолжить?" — просто выполняй код
- **ВАЖНО:** Секреты уже настроены, не трогай Vault API. Фокус только на коде агента.

---

## КОНТЕКСТ

### Сервер: 78.17.24.96 (LEVIATHAN)
### Основной проект: /opt/leviathan_engine/agent_service/ (~25K строк кода)

### Текущее состояние (из анализа MY_SYSTEM_ANALYSIS.md):
- ✅ Есть: ContextKeeper, OperationRegistry, EventBus, Tools (13 файлов)
- ❌ Нет: State Machine, Runnable Interface, Middleware Pipeline, Callback System, Workflow Engine
- ⚠️ Частично: Memory (только SQLite), Security (базовый PII)

### Цель: Превратить Leviathan Engine в полноценную систему уровня Манифест 5.1.0

---

## ЭТАП 0: ВОССТАНОВЛЕНИЕ КОНТЕКСТА

Прочитай эти файлы ПЕРВЫМИ:
1. /root/analysis/MY_SYSTEM_ANALYSIS.md — детальный разбор архитектуры
2. /root/implementation_log.md — что уже сделано (если есть)
3. /opt/leviathan_engine/agent_service/agent/core.py — текущий код агента
4. /opt/leviathan_engine/agent_service/agent/dispatcher.py — текущий диспетчер

Проверь, что уже реализовано:
```bash
grep -r "class StateMachine" /opt/leviathan_engine/ --include="*.py" 2>/dev/null || echo "❌ StateMachine нет"
grep -r "class Runnable" /opt/leviathan_engine/ --include="*.py" 2>/dev/null || echo "❌ Runnable нет"
grep -r "class MiddlewarePipeline" /opt/leviathan_engine/ --include="*.py" 2>/dev/null || echo "❌ Middleware нет"
ls /opt/leviathan_engine/agent_service/workflow/ 2>/dev/null || echo "❌ Workflow папки нет"
```

Определи последний завершённый этап и продолжи со следующего.

---

## ЭТАП 1: AGENT LIFECYCLE (FSM) — CRITICAL

**Цель:** Внедрить конечный автомат состояний вместо простых режимов Dispatcher.

### 1.1 Создание StateMachine
Создай файл `/opt/leviathan_engine/agent_service/agent/state.py`:

Реализуй классы:
- `AgentState` (Enum): INIT, PLAN, REASON, ACT, OBSERVE, REFLECT, LEARN, COMPLETE, FAILED, INTERRUPTED, PAUSED, RESUMED
- `StateTransition` (Dataclass): from_state, to_state, timestamp, metadata
- `StateMachine`:
  - Таблица валидных переходов (VALID_TRANSITIONS)
  - Метод `async transition(to_state, metadata)` с блокировкой (asyncio.Lock)
  - Метод `can_transition(to_state)`
  - История переходов (history list)

### 1.2 Интеграция в LeviathanAgent
Измени `/opt/leviathan_engine/agent_service/agent/core.py`:
- Импортируй `StateMachine`, `AgentState`
- В `__init__`: добавь `self.state_machine = StateMachine(self.agent_id)`
- В методе `run(task)`:
  - Перед планированием: `await self.state_machine.transition(AgentState.PLAN)`
  - Перед рассуждением: `await self.state_machine.transition(AgentState.REASON)`
  - Перед действием: `await self.state_machine.transition(AgentState.ACT)`
  - Перед наблюдением: `await self.state_machine.transition(AgentState.OBSERVE)`
  - При завершении: `await self.state_machine.transition(AgentState.COMPLETE)`
  - При ошибке: `await self.state_machine.transition(AgentState.FAILED)`
- Добавь логирование каждого перехода в основной логгер.

### 1.3 Интеграция в Dispatcher
Измени `/opt/leviathan_engine/agent_service/agent/dispatcher.py`:
- Добавь обработку состояний `PAUSED`, `RESUMED`, `INTERRUPTED`.
- Реализуй методы `pause()`, `resume()`, `interrupt()` которые меняют состояние через StateMachine.

**Отчёт:** Создай `/root/analysis/AGENT_STATE_IMPLEMENTATION.md` с примером использования и диаграммой переходов.

---

## ЭТАП 2: RUNNABLE INTERFACE — CRITICAL

**Цель:** Унифицировать интерфейс для Agent, Tools, Workflows.

### 2.1 Создание протокола Runnable
Создай файл `/opt/leviathan_engine/agent_service/agent/runnable.py`:

Реализуй абстрактный класс `Runnable[T, R]` с методами:
- `async invoke(input: T, config: Dict) -> R`
- `async ainvoke(input: T, config: Dict) -> R` (alias)
- `async stream(input: T, config: Dict) -> AsyncIterator[R]`
- `async batch(inputs: List[T], config: Dict) -> List[R]`
- Оператор композиции `__or__` для создания цепочек (`RunnableSequence`).

Реализуй класс `RunnableSequence`:
- Принимает список шагов.
- Последовательно вызывает `invoke` для каждого шага, передавая результат предыдущего.

### 2.2 Обёртка компонентов
Сделай существующие компоненты наследниками `Runnable`:
- `LeviathanAgent` implements `Runnable[Task, Response]`
- `Tool` (базовый класс инструментов) implements `Runnable[ToolInput, ToolOutput]`
- `Dispatcher` implements `Runnable[Message, Message]`

Обнови сигнатуры методов в `/opt/leviathan_engine/agent_service/agent/tools/*.py` чтобы они соответствовали интерфейсу.

**Отчёт:** Создай `/root/analysis/RUNNABLE_IMPLEMENTATION.md` с примерами цепочек (Agent | Tool | Validator).

---

## ЭТАП 3: MIDDLEWARE PIPELINE — CRITICAL

**Цель:** Добавить точки расширения (hooks) для логирования, безопасности, метрик.

### 3.1 Создание Pipeline
Создай файл `/opt/leviathan_engine/agent_service/middleware/pipeline.py`:

Реализуй:
- Enum `MiddlewareHook`: BEFORE_AGENT, BEFORE_MODEL, MODIFY_MODEL_REQUEST, AFTER_MODEL, WRAP_TOOL_CALL, AFTER_TOOL_CALL.
- Класс `MiddlewareContext`: agent_id, state, request, metadata.
- Базовый класс `Middleware` с пустыми методами хуков (async).
- Класс `MiddlewarePipeline`:
  - Список зарегистрированных мидлварей.
  - Метод `add(middleware)`.
  - Метод `execute_hook(hook, context, *args)` который проходит по всем мидлварям.

### 3.2 Реализация базовых мидлварей
Создай конкретные реализации в `/opt/leviathan_engine/agent_service/middleware/`:
- `LoggingMiddleware`: логирует вход/выход из модели и инструментов.
- `RateLimitMiddleware`: считает запросы, выбрасывает ошибку при превышении лимита.
- `SecurityMiddleware`: проверяет ввод на PII и опасные паттерны (используй существующие утилиты из tools_security.py).
- `MetricsMiddleware`: замеряет время выполнения и количество токенов.

### 3.3 Интеграция
Внедри вызовы `pipeline.execute_hook` в ключевые места `LeviathanAgent.run()`:
- Перед отправкой в LLM: `BEFORE_MODEL`, `MODIFY_MODEL_REQUEST`.
- После ответа LLM: `AFTER_MODEL`.
- Перед вызовом инструмента: `WRAP_TOOL_CALL`.
- После результата инструмента: `AFTER_TOOL_CALL`.

**Отчёт:** Создай `/root/analysis/MIDDLEWARE_IMPLEMENTATION.md` со списком хуков и примером кастомной мидлвари.

---

## ЭТАП 4: CALLBACK SYSTEM — HIGH

**Цель:** Расширить EventBus до полноценной системы событий.

### 4.1 Создание CallbackManager
Создай файл `/opt/leviathan_engine/agent_service/callbacks/manager.py`:

Реализуй класс `CallbackManager` с регистрацией обработчиков для событий:
- `on_llm_start/end/error`
- `on_tool_start/end/error`
- `on_agent_start/end/error`
- `on_state_transition` (интеграция с Этапом 1)
- `on_checkpoint_save/load`

Используй асинхронные callback-и. Если событие не обрабатывается, игнорируй его (не ломайте поток).

### 4.2 Интеграция с существующим EventBus
Замени или оберни старый `EventBus` новым `CallbackManager`.
Убедись, что все старые подписчики продолжают работать.

Добавь триггеры событий в код агента (в местах, где уже внедрены хуки Middleware).

**Отчёт:** Создай `/root/analysis/CALLBACK_IMPLEMENTATION.md` со списком всех событий и примером подписки.

---

## ЭТАП 5: WORKFLOW ENGINE — HIGH

**Цель:** Добавить поддержку сложных потоков выполнения (Branch, Merge, Parallel).

### 5.1 Создание WorkflowEngine
Создай файл `/opt/leviathan_engine/agent_service/workflow/engine.py`:

Реализуй:
- Класс `WorkflowStep`: обёртка над Runnable с условиями перехода.
- Класс `WorkflowGraph`: ориентированный граф шагов.
- Методы навигации: `add_branch(condition)`, `add_merge()`, `add_parallel(steps)`.
- Метод `async execute(start_step, initial_state)`: обход графа с сохранением состояния.

### 5.2 Декораторы Flow (опционально, но желательно)
Создай декораторы `@step`, `@branch`, `@router` для декларативного описания воркфлоу (как в CrewAI).

### 5.3 Интеграция с Checkpointing
Используй существующий `ContextKeeper` для сохранения состояния воркфлоу между шагами, чтобы поддерживать resume после падения.

**Отчёт:** Создай `/root/analysis/WORKFLOW_IMPLEMENTATION.md` с примером сложного воркфлоу (Plan -> Branch(A/B) -> Merge -> Complete).

---

## ЭТАП 6: MEMORY SYSTEM V2 — HIGH

**Цель:** Разделить память на типы вместо одного монолита.

### 6.1 Рефакторинг памяти
Создай файл `/opt/leviathan_engine/agent_service/db/memory_system.py`:

Реализуй классы:
- `ShortTermMemory`: кэш последних N сообщений (TTL 30 мин).
- `LongTermMemory`: интеграция с векторным поиском (если есть база знаний) или просто архив старых диалогов.
- `EpisodicMemory`: журнал событий (кто, что, когда) — используй существующий `context_ledger`.
- `WorkingMemory`: текущий контекст задачи (переменные, промежуточные результаты).
- `SemanticMemory`: факты о мире (из knowledge_base).

Создай фасад `MemoryManager`, который объединяет их и предоставляет единый интерфейс `get_context(query)`.

### 6.2 Миграция
Перенеси логику из `db/context_memory.py` в новые классы. Обнови вызовы в агенте.

**Отчёт:** Создай `/root/analysis/MEMORY_IMPLEMENTATION.md` с описанием стратегии eviction и поиска для каждого типа.

---

## ЭТАП 7: ФИНАЛИЗАЦИЯ И ТЕСТИРОВАНИЕ

### 7.1 Smoke Tests
Напиши простой скрипт `/opt/leviathan_engine/tests/test_manifest_5.1.py`:
- Проверка перехода состояний (StateMachine).
- Проверка работы цепочки Runnable.
- Проверка срабатывания Middleware.
- Проверка сохранения состояния Workflow.

### 7.2 Финальный отчёт
Создай `/root/analysis/MANIFEST_5.1_FINAL_REPORT.md`:
1. **Executive Summary**: Что реализовано, что улучшено.
2. **Архитектура**: Схема новых компонентов.
3. **Метрики**: Количество новых файлов, строк кода, покрытие тестами.
4. **Инструкция**: Как запустить обновлённого агента.
5. **План дальнейших улучшений**: Plugin SDK, Multi-Agent, Sandbox (оставь на будущее).

---

## ЛОГИРОВАНИЕ (ОБЯЗАТЕЛЬНО)

В начале каждого этапа пиши в `/root/implementation_log.md`:
```markdown
## [Дата Время] ЭТАП N: Название
- Старт: Анализ текущих файлов...
- Действие: Создание файла X...
- Действие: Изменение файла Y...
- Результат: Успех/Ошибка (с деталями)
- Файлы созданы: [...]
```

---

## НАЧНИ НЕМЕДЛЕННО

1. Прочитай контекст (Этап 0).
2. Начни с **Этапа 1 (Agent Lifecycle)**. Это фундамент.
3. Не останавливайся, пока не пройдёшь все 7 этапов.
4. Пиши рабочий код сразу, без лишних объяснений в чате.
5. Если встречаешь ошибку — исправляй её или обходи, но двигайся дальше.

**Вперёд! Реализуй Манифест 5.1.0!**
