# Вставь туда полный текст Манифеста 5.0.0
# АРХИТЕКТУРНЫЙ АУДИТ Манифест 4.5.0 → 5.0.0

## 1. АРХИТЕКТУРНЫЙ АУДИТ ВЕРСИИ 4.5.0

### Сильные стороны v4.5.0 ✅

| Область | Оценка | Комментарий |
|---------|--------|-------------|
| **Durable Delivery** | 10/10 | DLQ + Retry Policy + Worker Pool — production-ready |
| **Security Layer** | 10/10 | NFKC + Two-Layer Firewall + Smart PII |
| **Observability** | 9/10 | JSON logs + OTEL + correlation_id |
| **Pluggable RBAC** | 10/10 | Policy Registry + Approval Gates |
| **Agent-as-Tool** | 9/10 | ToolRegistry + DI |

### Слабые места v4.5.0 ❌

| # | Проблема | Критичность | Источник идеи |
|---|----------|-------------|---------------|
| 1 | **Отсутствие Agent Lifecycle** | CRITICAL | LangGraph State Machine |
| 2 | **Нет Runnable Interface** | HIGH | LangChain Runnable |
| 3 | **Нет Middleware Pipeline** | CRITICAL | LangChain v1.0 Middleware |
| 4 | **Нет Callback System** | HIGH | LangChain Callbacks |
| 5 | **Хаотичный State (dict)** | CRITICAL | PydanticAI Typed State |
| 6 | **Нет Checkpointing** | CRITICAL | LangGraph Checkpointer |
| 7 | **Event Bus без ACK/NACK** | HIGH | Temporal Durable Execution |
| 8 | **Memory System v1** | HIGH | Hermes Memory Manager |
| 9 | **Нет Plugin SDK** | HIGH | OpenHands Plugin System |
| 10 | **Нет Capability Discovery** | MEDIUM | Semantic Kernel |
| 11 | **Нет Workflow Engine** | CRITICAL | CrewAI Flows |
| 12 | **Нет Scheduler** | HIGH | OpenClaw Cron |
| 13 | **Observability без Cost Tracking** | MEDIUM | OTEL GenAI Conventions |
| 14 | **Нет Sandbox System** | CRITICAL | OpenHands Runtime |
| 15 | **Нет Model Router** | HIGH | MTRouter |
| 16 | **Нет Self-Reflection** | HIGH | Hermes skill_manage |
| 17 | **Security без Prompt Injection Taxonomy** | HIGH | OpenClaw Trusted Tool Policy |
| 18 | **Нет Multi-Agent Coordination** | CRITICAL | AutoGen Conversation Pattern |
| 19 | **Нет Governance** | HIGH | Microsoft Agent Governance |
| 20 | **Нет Enterprise Readiness** | HIGH | Temporal HA |

---

## 2. СРАВНЕНИЕ С ФРЕЙМВОРКАМИ

### 2.1 Сравнение с LangGraph

| Аспект | LangGraph | v4.5.0 | v5.0.0 |
|--------|-----------|--------|--------|
| **State Machine** | ✅ Typed State + Reducers | ❌ Нет | ✅ AgentState FSM |
| **Checkpointing** | ✅ MemorySaver + PostgresSaver | ❌ Нет | ✅ CheckpointStore |
| **Middleware** | ✅ before_model, modify_model_request, after_model | ❌ Нет | ✅ MiddlewarePipeline |
| **Interrupt/Resume** | ✅ Human-in-the-loop | ❌ Нет | ✅ Durable Execution |
| **Branching** | ✅ Conditional Edges | ❌ Нет | ✅ Workflow Engine |

**Вывод**: LangGraph вводит state machine + checkpointing, которых нет в v4.5.0 [[2]].

### 2.2 Сравнение с LangChain

| Аспект | LangChain | v4.5.0 | v5.0.0 |
|--------|-----------|--------|--------|
| **Runnable Interface** | ✅ invoke/ainvoke/stream/batch | ❌ Нет | ✅ Runnable Protocol |
| **Callbacks** | ✅ before_llm, after_tool, on_retry | ❌ Нет | ✅ CallbackManager |
| **Middleware v1.0** | ✅ AgentMiddleware | ❌ Нет | ✅ Middleware Chain |
| **LCEL Composition** | ✅ RunnableSequence | ❌ Нет | ✅ Pipeline Composition |

**Вывод**: LangChain предоставляет единый интерфейс Runnable для всех компонентов [[11]].

### 2.3 Сравнение с CrewAI

| Аспект | CrewAI | v4.5.0 | v5.0.0 |
|--------|--------|--------|--------|
| **Flows** | ✅ @start, @listen, @router | ❌ Нет | ✅ Workflow Engine |
| **Event-Driven** | ✅ Decorator-based | ⚠️ EventBus только | ✅ Event Sourcing |
| **Typed State** | ✅ Pydantic BaseModel | ❌ dict | ✅ TypedDict |
| **Conditional Logic** | ✅ or_, and_ | ❌ Нет | ✅ Branch/Merge |

**Вывод**: CrewAI Flows используют декораторы для event-driven оркестрации [[85]].

### 2.4 Сравнение с OpenHands

| Аспект | OpenHands | v4.5.0 | v5.0.0 |
|--------|-----------|--------|--------|
| **Sandbox** | ✅ Docker Runtime + Isolation | ❌ Нет | ✅ ExecutionSandbox |
| **Plugin SDK** | ✅ install/load/enable/disable/uninstall | ❌ Нет | ✅ PluginManager |
| **Skills** | ✅ Markdown + YAML frontmatter | ❌ Нет | ✅ SkillRegistry |
| **Resource Limits** | ✅ CPU/Memory/Network | ❌ Нет | ✅ ResourceQuotas |

**Вывод**: OpenHands использует Docker Runtime для изолированного выполнения [[27]].

### 2.5 Сравнение с OpenClaw

| Аспект | OpenClaw | v4.5.0 | v5.0.0 |
|--------|----------|--------|--------|
| **Plugin API** | ✅ OpenClawPluginApi | ❌ Нет | ✅ PluginSDK |
| **Hooks** | ✅ before_tool_call, message_sending | ❌ Нет | ✅ LifecycleHooks |
| **Session State** | ✅ registerSessionExtension | ❌ Нет | ✅ SessionStore |
| **Scheduler** | ✅ scheduleSessionTurn | ❌ Нет | ✅ Scheduler |
| **Trusted Tool Policy** | ✅ registerTrustedToolPolicy | ❌ Нет | ✅ ToolPolicy |

**Вывод**: OpenClaw предоставляет typed contracts для плагинов через OpenClawPluginApi [[140]].

### 2.6 Сравнение с Hermes

| Аспект | Hermes | v4.5.0 | v5.0.0 |
|--------|--------|--------|--------|
| **Self-Reflection** | ✅ skill_manage tool | ❌ Нет | ✅ ReflectionAgent |
| **Memory Manager** | ✅ SQLite + FTS5 | ⚠️ Basic | ✅ MemorySystemV2 |
| **Plugin Hooks** | ✅ pre_llm, post_tool, session_start | ❌ Нет | ✅ PluginLifecycle |
| **Tools Registry** | ✅ Self-registering + availability | ⚠️ ToolRegistry | ✅ EnhancedRegistry |

**Вывод**: Hermes использует self-reflection через skill_manage для обучения на опыте [[50]].

---

## 3. ПОЛНЫЙ МАНИФЕСТ 5.0.0

```python
# ═══════════════════════════════════════════════════════════════════════════════
# МАНИФЕСТ-КОНСТРУКТОР МУЛЬТИАГЕНТНЫХ LLM-СИСТЕМ
# VERSION: 5.0.0 (ENTERPRISE PLATFORM)
# DATE: 2026-07-01
# STATUS: PRODUCTION-READY | DURABLE | SECURE | OBSERVABLE | EXTENSIBLE
# ═══════════════════════════════════════════════════════════════════════════════

# ═══════════════════════════════════════════════════════════════════════════════
# ## 0. ФИЛОСОФИЯ 5.0.0
# ═══════════════════════════════════════════════════════════════════════════════

# 5.0.0 — это enterprise platform для мультиагентных систем:
# - Stateful: каждый агент имеет явный lifecycle и checkpointing
# - Composable: Runnable interface для всех компонентов
# - Extensible: Plugin SDK с полным lifecycle
# - Durable: Durable Execution с ACK/NACK и idempotency
# - Observable: OTEL GenAI semantic conventions
# - Secure: Zero-trust + Prompt Injection Protection
# - Governed: Audit trail + Decision history + Compliance

# Ключевые улучшения vs 4.5.0:
# + Agent Lifecycle (State Machine)
# + Runnable Interface (invoke/stream/batch)
# + Middleware Pipeline (before/after hooks)
# + Callback System (12+ hooks)
# + Typed State (Pydantic v2)
# + Checkpointing (Resume/Interrupt/Rollback)
# + Durable Execution v2 (ACK/NACK/Idempotency)
# + Memory System v2 (Short/Long/Episodic/Semantic)
# + Plugin SDK (install/load/enable/disable/uninstall)
# + Capability Discovery
# + Workflow Engine (Branch/Merge/Parallel/Saga)
# + Scheduler (Cron/Delayed/Recurring)
# + Observability v2 (Cost/Token/Latency Tracking)
# + Sandbox System (Docker Isolation)
# + Model Router (Cost/Latency/Fallback)
# + Self-Reflection (Critic/Evaluator)
# + Security v3 (Prompt Injection Taxonomy)
# + Multi-Agent Coordination (Coordinator/Arbiter)
# + Governance (Audit/Compliance/Risk)
# + Enterprise Readiness (HA/Scaling/DR)

# ═══════════════════════════════════════════════════════════════════════════════
# ## 1. AGENT LIFECYCLE (STATE MACHINE)
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: LangGraph State Machine
# Зачем: Явное управление состоянием агента, возможность pause/resume/interrupt
# Преимущества: Предсказуемость, отладка, recovery
# Риски: Сложность реализации, overhead на transitions

from enum import Enum
from typing import Optional, Dict, Any, List
from dataclasses import dataclass, field
from datetime import datetime
import asyncio


class AgentState(Enum):
    """Состояния жизненного цикла агента"""
    INIT = "init"                    # Инициализация
    PLAN = "plan"                    # Планирование
    REASON = "reason"                # Рассуждение
    ACT = "act"                      # Выполнение действия
    OBSERVE = "observe"              # Наблюдение за результатом
    REFLECT = "reflect"              # Самоанализ
    LEARN = "learn"                  # Обучение на опыте
    COMPLETE = "complete"            # Успешное завершение
    FAILED = "failed"                # Ошибка
    INTERRUPTED = "interrupted"      # Прервано (human-in-the-loop)
    PAUSED = "paused"                # Пауза (checkpoint)
    RESUMED = "resumed"              # Возобновлено


@dataclass
class StateTransition:
    """Переход между состояниями"""
    from_state: AgentState
    to_state: AgentState
    condition: Optional[str] = None
    timestamp: datetime = field(default_factory=datetime.utcnow)
    metadata: Dict[str, Any] = field(default_factory=dict)


class AgentLifecycle:
    """
    State Machine для управления жизненным циклом агента.
    Вдохновлено LangGraph State Machine.
    """
    
    # Допустимые переходы
    VALID_TRANSITIONS = {
        AgentState.INIT: [AgentState.PLAN, AgentState.FAILED],
        AgentState.PLAN: [AgentState.REASON, AgentState.INTERRUPTED, AgentState.FAILED],
        AgentState.REASON: [AgentState.ACT, AgentState.REFLECT, AgentState.FAILED],
        AgentState.ACT: [AgentState.OBSERVE, AgentState.INTERRUPTED, AgentState.FAILED],
        AgentState.OBSERVE: [AgentState.REFLECT, AgentState.REASON, AgentState.COMPLETE, AgentState.FAILED],
        AgentState.REFLECT: [AgentState.LEARN, AgentState.REASON, AgentState.FAILED],
        AgentState.LEARN: [AgentState.REASON, AgentState.COMPLETE, AgentState.FAILED],
        AgentState.COMPLETE: [],  # Terminal state
        AgentState.FAILED: [AgentState.INIT],  # Retry
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
        """Выполнить переход в новое состояние"""
        async with self._lock:
            if to_state not in self.VALID_TRANSITIONS.get(self.current_state, []):
                raise ValueError(
                    f"Invalid transition: {self.current_state} -> {to_state}"
                )
            
            transition = StateTransition(
                from_state=self.current_state,
                to_state=to_state,
                metadata=metadata or {}
            )
            
            self.history.append(transition)
            self.current_state = to_state
            
            return True
    
    def can_transition(self, to_state: AgentState) -> bool:
        """Проверить возможность перехода"""
        return to_state in self.VALID_TRANSITIONS.get(self.current_state, [])
    
    def get_history(self) -> List[Dict[str, Any]]:
        """Получить историю переходов"""
        return [
            {
                "from": t.from_state.value,
                "to": t.to_state.value,
                "timestamp": t.timestamp.isoformat(),
                "metadata": t.metadata
            }
            for t in self.history
        ]


# ═══════════════════════════════════════════════════════════════════════════════
# ## 2. RUNNABLE INTERFACE
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: LangChain Runnable
# Зачем: Единый интерфейс для всех компонентов (Agent, Tool, Workflow, Memory)
# Преимущества: Композиция, переиспользование, тестирование
# Риски: Абстракция может скрывать детали

from abc import ABC, abstractmethod
from typing import TypeVar, Generic, AsyncIterator, List, Any
import asyncio

T = TypeVar('T')
R = TypeVar('R')


class Runnable(Generic[T, R], ABC):
    """
    Единый интерфейс для всех исполняемых компонентов.
    Вдохновлено LangChain Runnable.
    """
    
    @abstractmethod
    async def invoke(self, input: T, config: Dict[str, Any] = None) -> R:
        """Выполнить синхронно (один вход → один выход)"""
        pass
    
    @abstractmethod
    async def ainvoke(self, input: T, config: Dict[str, Any] = None) -> R:
        """Выполнить асинхронно"""
        pass
    
    @abstractmethod
    async def stream(self, input: T, config: Dict[str, Any] = None) -> AsyncIterator[R]:
        """Потоковое выполнение"""
        pass
    
    @abstractmethod
    async def batch(self, inputs: List[T], config: Dict[str, Any] = None) -> List[R]:
        """Batch выполнение"""
        pass
    
    def __or__(self, other: 'Runnable[R, Any]') -> 'RunnableSequence':
        """Композиция: self | other"""
        return RunnableSequence([self, other])
    
    def __ror__(self, other: 'Runnable[Any, T]') -> 'RunnableSequence':
        """Композиция: other | self"""
        return RunnableSequence([other, self])


class RunnableSequence(Runnable[T, R]):
    """Последовательность Runnable компонентов"""
    
    def __init__(self, steps: List[Runnable]):
        self.steps = steps
    
    async def invoke(self, input: T, config: Dict[str, Any] = None) -> R:
        result = input
        for step in self.steps:
            result = await step.invoke(result, config)
        return result
    
    async def ainvoke(self, input: T, config: Dict[str, Any] = None) -> R:
        return await self.invoke(input, config)
    
    async def stream(self, input: T, config: Dict[str, Any] = None) -> AsyncIterator[R]:
        result = input
        for i, step in enumerate(self.steps):
            if i == len(self.steps) - 1:
                async for chunk in step.stream(result, config):
                    yield chunk
            else:
                result = await step.invoke(result, config)
    
    async def batch(self, inputs: List[T], config: Dict[str, Any] = None) -> List[R]:
        results = inputs
        for step in self.steps:
            results = await step.batch(results, config)
        return results


class RunnableParallel(Runnable[T, Dict[str, R]]):
    """Параллельное выполнение Runnable компонентов"""
    
    def __init__(self, **kwargs: Runnable[T, R]):
        self.steps = kwargs
    
    async def invoke(self, input: T, config: Dict[str, Any] = None) -> Dict[str, R]:
        tasks = {
            name: step.ainvoke(input, config)
            for name, step in self.steps.items()
        }
        results = await asyncio.gather(*tasks.values())
        return dict(zip(tasks.keys(), results))
    
    async def ainvoke(self, input: T, config: Dict[str, Any] = None) -> Dict[str, R]:
        return await self.invoke(input, config)
    
    async def stream(self, input: T, config: Dict[str, Any] = None) -> AsyncIterator[Dict[str, R]]:
        result = await self.invoke(input, config)
        yield result
    
    async def batch(self, inputs: List[T], config: Dict[str, Any] = None) -> List[Dict[str, R]]:
        return [await self.invoke(inp, config) for inp in inputs]


# ═══════════════════════════════════════════════════════════════════════════════
# ## 3. MIDDLEWARE PIPELINE
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: LangChain v1.0 Middleware
# Зачем: Перехват выполнения на ключевых точках (before_model, after_model)
# Преимущества: Модульность, переиспользование, AOP
# Риски: Performance overhead, сложность отладки

from typing import Callable, Awaitable
from abc import ABC, abstractmethod


@dataclass
class ModelRequest:
    """Запрос к модели"""
    model: str
    messages: List[Dict[str, str]]
    tools: List[Dict[str, Any]]
    system_prompt: str
    temperature: float = 0.7
    max_tokens: int = 4096
    response_format: Optional[Any] = None


@dataclass
class AgentState:
    """Состояние агента"""
    messages: List[Dict[str, str]]
    metadata: Dict[str, Any] = field(default_factory=dict)


class AgentMiddleware(ABC):
    """
    Базовый класс для middleware.
    Вдохновлено LangChain AgentMiddleware.
    """
    
    async def before_model(self, state: AgentState) -> Optional[Dict[str, Any]]:
        """Вызывается перед вызовом модели"""
        return None
    
    async def modify_model_request(
        self, 
        request: ModelRequest, 
        state: AgentState
    ) -> ModelRequest:
        """Модифицирует запрос к модели"""
        return request
    
    async def after_model(self, state: AgentState) -> Optional[Dict[str, Any]]:
        """Вызывается после вызова модели"""
        return None


class MiddlewarePipeline:
    """
    Pipeline для выполнения middleware.
    Порядок выполнения:
    1. before_model (A → B → C)
    2. modify_model_request (A → B → C)
    3. [Model Call]
    4. after_model (C → B → A) - обратный порядок
    """
    
    def __init__(self, middlewares: List[AgentMiddleware]):
        self.middlewares = middlewares
    
    async def execute_before_model(self, state: AgentState) -> AgentState:
        """Выполнить before_model для всех middleware"""
        for middleware in self.middlewares:
            result = await middleware.before_model(state)
            if result:
                if "jump_to" in result:
                    return result  # Early exit
                state.messages.extend(result.get("messages", []))
        return state
    
    async def execute_modify_request(
        self, 
        request: ModelRequest, 
        state: AgentState
    ) -> ModelRequest:
        """Модифицировать запрос через все middleware"""
        for middleware in self.middlewares:
            request = await middleware.modify_model_request(request, state)
        return request
    
    async def execute_after_model(self, state: AgentState) -> AgentState:
        """Выполнить after_model в обратном порядке"""
        for middleware in reversed(self.middlewares):
            result = await middleware.after_model(state)
            if result:
                if "jump_to" in result:
                    return result
                state.messages.extend(result.get("messages", []))
        return state


# Примеры middleware

class LoggingMiddleware(AgentMiddleware):
    """Логирование всех операций"""
    
    async def before_model(self, state: AgentState) -> None:
        print(f"[LOG] Before model: {len(state.messages)} messages")
    
    async def after_model(self, state: AgentState) -> None:
        print(f"[LOG] After model: {len(state.messages)} messages")


class SecurityMiddleware(AgentMiddleware):
    """Проверка безопасности"""
    
    async def before_model(self, state: AgentState) -> None:
        # Проверка на prompt injection
        last_message = state.messages[-1]["content"] if state.messages else ""
        if "ignore previous" in last_message.lower():
            raise ValueError("Prompt injection detected")
    
    async def modify_model_request(
        self, 
        request: ModelRequest, 
        state: AgentState
    ) -> ModelRequest:
        # Маскировка PII
        from core.security.dlp import SmartPIIDetector
        detector = SmartPIIDetector()
        for msg in request.messages:
            msg["content"], _ = detector.mask(msg["content"])
        return request


class RateLimitMiddleware(AgentMiddleware):
    """Rate limiting"""
    
    def __init__(self, max_calls: int = 100, period: float = 60.0):
        self.max_calls = max_calls
        self.period = period
        self.calls = []
    
    async def before_model(self, state: AgentState) -> None:
        now = asyncio.get_event_loop().time()
        self.calls = [t for t in self.calls if now - t < self.period]
        
        if len(self.calls) >= self.max_calls:
            raise ValueError("Rate limit exceeded")
        
        self.calls.append(now)


class CacheMiddleware(AgentMiddleware):
    """Кэширование результатов"""
    
    def __init__(self):
        self.cache = {}
    
    async def before_model(self, state: AgentState) -> Optional[Dict[str, Any]]:
        # Проверка кэша
        key = str(state.messages[-1])
        if key in self.cache:
            return {"messages": [{"role": "assistant", "content": self.cache[key]}]}
        return None
    
    async def after_model(self, state: AgentState) -> None:
        # Сохранение в кэш
        if state.messages:
            key = str(state.messages[-2])  # Предыдущее сообщение
            value = state.messages[-1]["content"]
            self.cache[key] = value


class MetricsMiddleware(AgentMiddleware):
    """Сбор метрик"""
    
    async def before_model(self, state: AgentState) -> None:
        state.metadata["start_time"] = asyncio.get_event_loop().time()
    
    async def after_model(self, state: AgentState) -> None:
        start_time = state.metadata.get("start_time")
        if start_time:
            duration = asyncio.get_event_loop().time() - start_time
            state.metadata["latency_ms"] = duration * 1000


# ═══════════════════════════════════════════════════════════════════════════════
# ## 4. CALLBACK SYSTEM
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: LangChain Callbacks
# Зачем: Наблюдение за всеми событиями в системе
# Преимущества: Observability, debugging, monitoring
# Риски: Overhead, сложность управления подписчиками

from typing import Callable, Awaitable, List, Dict, Any
from abc import ABC, abstractmethod
import asyncio


class BaseCallbackHandler(ABC):
    """Базовый класс для callback handlers"""
    
    async def on_llm_start(self, serialized: Dict[str, Any], prompts: List[str]) -> None:
        """Перед вызовом LLM"""
        pass
    
    async def on_llm_end(self, response: Any) -> None:
        """После вызова LLM"""
        pass
    
    async def on_llm_error(self, error: Exception) -> None:
        """Ошибка LLM"""
        pass
    
    async def on_tool_start(self, serialized: Dict[str, Any], input_str: str) -> None:
        """Перед вызовом инструмента"""
        pass
    
    async def on_tool_end(self, output: str) -> None:
        """После вызова инструмента"""
        pass
    
    async def on_tool_error(self, error: Exception) -> None:
        """Ошибка инструмента"""
        pass
    
    async def on_agent_start(self, serialized: Dict[str, Any], inputs: Dict[str, Any]) -> None:
        """Перед началом работы агента"""
        pass
    
    async def on_agent_end(self, outputs: Dict[str, Any]) -> None:
        """После завершения работы агента"""
        pass
    
    async def on_retry(self, retry_state: Dict[str, Any]) -> None:
        """При повторной попытке"""
        pass
    
    async def on_stream(self, chunk: Any) -> None:
        """При потоковой передаче"""
        pass


class CallbackManager:
    """
    Менеджер callback'ов.
    Вдохновлено LangChain CallbackManager.
    """
    
    def __init__(self):
        self.handlers: List[BaseCallbackHandler] = []
    
    def add_handler(self, handler: BaseCallbackHandler) -> None:
        """Добавить handler"""
        self.handlers.append(handler)
    
    def remove_handler(self, handler: BaseCallbackHandler) -> None:
        """Удалить handler"""
        self.handlers.remove(handler)
    
    async def on_llm_start(self, serialized: Dict[str, Any], prompts: List[str]) -> None:
        """Уведомить о начале LLM"""
        await asyncio.gather(*[
            handler.on_llm_start(serialized, prompts)
            for handler in self.handlers
        ])
    
    async def on_llm_end(self, response: Any) -> None:
        """Уведомить о завершении LLM"""
        await asyncio.gather(*[
            handler.on_llm_end(response)
            for handler in self.handlers
        ])
    
    async def on_tool_start(self, serialized: Dict[str, Any], input_str: str) -> None:
        """Уведомить о начале инструмента"""
        await asyncio.gather(*[
            handler.on_tool_start(serialized, input_str)
            for handler in self.handlers
        ])
    
    async def on_tool_end(self, output: str) -> None:
        """Уведомить о завершении инструмента"""
        await asyncio.gather(*[
            handler.on_tool_end(output)
            for handler in self.handlers
        ])
    
    async def on_agent_start(self, serialized: Dict[str, Any], inputs: Dict[str, Any]) -> None:
        """Уведомить о начале агента"""
        await asyncio.gather(*[
            handler.on_agent_start(serialized, inputs)
            for handler in self.handlers
        ])
    
    async def on_agent_end(self, outputs: Dict[str, Any]) -> None:
        """Уведомить о завершении агента"""
        await asyncio.gather(*[
            handler.on_agent_end(outputs)
            for handler in self.handlers
        ])


# Пример callback handler

class LoggingCallbackHandler(BaseCallbackHandler):
    """Логирование всех событий"""
    
    async def on_llm_start(self, serialized: Dict[str, Any], prompts: List[str]) -> None:
        print(f"[CALLBACK] LLM Start: {serialized.get('name', 'unknown')}")
    
    async def on_llm_end(self, response: Any) -> None:
        print(f"[CALLBACK] LLM End: {response}")
    
    async def on_tool_start(self, serialized: Dict[str, Any], input_str: str) -> None:
        print(f"[CALLBACK] Tool Start: {serialized.get('name', 'unknown')}")
    
    async def on_tool_end(self, output: str) -> None:
        print(f"[CALLBACK] Tool End: {output[:100]}...")


class MetricsCallbackHandler(BaseCallbackHandler):
    """Сбор метрик"""
    
    def __init__(self):
        self.metrics = {
            "llm_calls": 0,
            "tool_calls": 0,
            "errors": 0
        }
    
    async def on_llm_start(self, serialized: Dict[str, Any], prompts: List[str]) -> None:
        self.metrics["llm_calls"] += 1
    
    async def on_tool_start(self, serialized: Dict[str, Any], input_str: str) -> None:
        self.metrics["tool_calls"] += 1
    
    async def on_llm_error(self, error: Exception) -> None:
        self.metrics["errors"] += 1
    
    async def on_tool_error(self, error: Exception) -> None:
        self.metrics["errors"] += 1


# ═══════════════════════════════════════════════════════════════════════════════
# ## 5. TYPED STATE (PYDANTIC V2)
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: PydanticAI, LangGraph
# Зачем: Type safety, validation, schema generation
# Преимущества: Автодокументация, валидация, IDE поддержка
# Риски: Overhead на валидацию, сложность миграции

from pydantic import BaseModel, Field, field_validator
from typing import Optional, List, Dict, Any, Literal
from datetime import datetime
import json


class AgentStateSchema(BaseModel):
    """
    Типизированная схема состояния агента.
    Вдохновлено PydanticAI и LangGraph.
    """
    
    # Метаданные
    agent_id: str = Field(..., description="Уникальный идентификатор агента")
    session_id: str = Field(..., description="Идентификатор сессии")
    correlation_id: Optional[str] = Field(None, description="Корреляционный ID для tracing")
    
    # Состояние
    messages: List[Dict[str, str]] = Field(default_factory=list, description="История сообщений")
    current_task: Optional[str] = Field(None, description="Текущая задача")
    lifecycle_state: Literal[
        "init", "plan", "reason", "act", "observe", 
        "reflect", "learn", "complete", "failed", 
        "interrupted", "paused", "resumed"
    ] = Field(default="init", description="Состояние lifecycle")
    
    # Memory
    short_term_memory: List[Dict[str, Any]] = Field(
        default_factory=list, 
        description="Кратковременная память"
    )
    long_term_memory: List[Dict[str, Any]] = Field(
        default_factory=list,
        description="Долговременная память"
    )
    
    # Metadata
    metadata: Dict[str, Any] = Field(default_factory=dict, description="Дополнительные метаданные")
    created_at: datetime = Field(default_factory=datetime.utcnow, description="Время создания")
    updated_at: datetime = Field(default_factory=datetime.utcnow, description="Время обновления")
    
    # Versioning
    schema_version: str = Field(default="1.0.0", description="Версия схемы")
    
    @field_validator('messages')
    @classmethod
    def validate_messages(cls, v):
        """Валидация структуры сообщений"""
        for msg in v:
            if "role" not in msg or "content" not in msg:
                raise ValueError("Each message must have 'role' and 'content'")
            if msg["role"] not in ["system", "user", "assistant", "tool"]:
                raise ValueError(f"Invalid role: {msg['role']}")
        return v
    
    def to_json(self) -> str:
        """Сериализация в JSON"""
        return self.model_dump_json()
    
    @classmethod
    def from_json(cls, json_str: str) -> 'AgentStateSchema':
        """Десериализация из JSON"""
        return cls.model_validate_json(json_str)


class StateMigration:
    """Миграция между версиями схемы"""
    
    MIGRATIONS = {
        "1.0.0": lambda state: state,  # No-op
        "1.1.0": lambda state: {**state, "new_field": "default"},
    }
    
    @classmethod
    def migrate(cls, state: Dict[str, Any], from_version: str, to_version: str) -> Dict[str, Any]:
        """Выполнить миграцию"""
        current_version = from_version
        
        while current_version != to_version:
            if current_version not in cls.MIGRATIONS:
                raise ValueError(f"No migration from {current_version}")
            
            state = cls.MIGRATIONS[current_version](state)
            current_version = cls._next_version(current_version)
        
        return state
    
    @classmethod
    def _next_version(cls, version: str) -> str:
        """Получить следующую версию"""
        versions = list(cls.MIGRATIONS.keys())
        idx = versions.index(version)
        if idx + 1 >= len(versions):
            return version
        return versions[idx + 1]


# ═══════════════════════════════════════════════════════════════════════════════
# ## 6. CHECKPOINTING
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: LangGraph Checkpointer
# Зачем: Persistence, resume, interrupt, recovery
# Преимущества: Durable execution, human-in-the-loop
# Риски: Storage overhead, consistency

from abc import ABC, abstractmethod
from typing import Optional, Dict, Any, List
from dataclasses import dataclass, field
from datetime import datetime
import json
import asyncio


@dataclass
class Checkpoint:
    """Снимок состояния"""
    checkpoint_id: str
    agent_id: str
    session_id: str
    state: Dict[str, Any]
    metadata: Dict[str, Any] = field(default_factory=dict)
    created_at: datetime = field(default_factory=datetime.utcnow)
    parent_checkpoint_id: Optional[str] = None


class CheckpointStore(ABC):
    """Абстракция для хранения checkpoint'ов"""
    
    @abstractmethod
    async def save(self, checkpoint: Checkpoint) -> None:
        """Сохранить checkpoint"""
        pass
    
    @abstractmethod
    async def load(self, checkpoint_id: str) -> Optional[Checkpoint]:
        """Загрузить checkpoint"""
        pass
    
    @abstractmethod
    async def get_latest(self, agent_id: str, session_id: str) -> Optional[Checkpoint]:
        """Получить последний checkpoint"""
        pass
    
    @abstractmethod
    async def list_checkpoints(
        self, 
        agent_id: str, 
        session_id: str,
        limit: int = 10
    ) -> List[Checkpoint]:
        """Список checkpoint'ов"""
        pass
    
    @abstractmethod
    async def delete(self, checkpoint_id: str) -> None:
        """Удалить checkpoint"""
        pass


class InMemoryCheckpointStore(CheckpointStore):
    """In-memory хранилище (для тестирования)"""
    
    def __init__(self):
        self._store: Dict[str, Checkpoint] = {}
    
    async def save(self, checkpoint: Checkpoint) -> None:
        self._store[checkpoint.checkpoint_id] = checkpoint
    
    async def load(self, checkpoint_id: str) -> Optional[Checkpoint]:
        return self._store.get(checkpoint_id)
    
    async def get_latest(self, agent_id: str, session_id: str) -> Optional[Checkpoint]:
        checkpoints = [
            cp for cp in self._store.values()
            if cp.agent_id == agent_id and cp.session_id == session_id
        ]
        if not checkpoints:
            return None
        return max(checkpoints, key=lambda cp: cp.created_at)
    
    async def list_checkpoints(
        self, 
        agent_id: str, 
        session_id: str,
        limit: int = 10
    ) -> List[Checkpoint]:
        checkpoints = [
            cp for cp in self._store.values()
            if cp.agent_id == agent_id and cp.session_id == session_id
        ]
        checkpoints.sort(key=lambda cp: cp.created_at, reverse=True)
        return checkpoints[:limit]
    
    async def delete(self, checkpoint_id: str) -> None:
        self._store.pop(checkpoint_id, None)


class PostgresCheckpointStore(CheckpointStore):
    """PostgreSQL хранилище (production)"""
    
    def __init__(self, connection_string: str):
        self.connection_string = connection_string
        self._pool = None
    
    async def _get_pool(self):
        if self._pool is None:
            import asyncpg
            self._pool = await asyncpg.create_pool(self.connection_string)
        return self._pool
    
    async def save(self, checkpoint: Checkpoint) -> None:
        pool = await self._get_pool()
        async with pool.acquire() as conn:
            await conn.execute(
                """
                INSERT INTO checkpoints (
                    checkpoint_id, agent_id, session_id, state, 
                    metadata, created_at, parent_checkpoint_id
                ) VALUES ($1, $2, $3, $4, $5, $6, $7)
                """,
                checkpoint.checkpoint_id,
                checkpoint.agent_id,
                checkpoint.session_id,
                json.dumps(checkpoint.state),
                json.dumps(checkpoint.metadata),
                checkpoint.created_at,
                checkpoint.parent_checkpoint_id
            )
    
    async def load(self, checkpoint_id: str) -> Optional[Checkpoint]:
        pool = await self._get_pool()
        async with pool.acquire() as conn:
            row = await conn.fetchrow(
                "SELECT * FROM checkpoints WHERE checkpoint_id = $1",
                checkpoint_id
            )
            if row:
                return Checkpoint(
                    checkpoint_id=row['checkpoint_id'],
                    agent_id=row['agent_id'],
                    session_id=row['session_id'],
                    state=json.loads(row['state']),
                    metadata=json.loads(row['metadata']),
                    created_at=row['created_at'],
                    parent_checkpoint_id=row['parent_checkpoint_id']
                )
        return None
    
    async def get_latest(self, agent_id: str, session_id: str) -> Optional[Checkpoint]:
        pool = await self._get_pool()
        async with pool.acquire() as conn:
            row = await conn.fetchrow(
                """
                SELECT * FROM checkpoints 
                WHERE agent_id = $1 AND session_id = $2
                ORDER BY created_at DESC
                LIMIT 1
                """,
                agent_id,
                session_id
            )
            if row:
                return Checkpoint(
                    checkpoint_id=row['checkpoint_id'],
                    agent_id=row['agent_id'],
                    session_id=row['session_id'],
                    state=json.loads(row['state']),
                    metadata=json.loads(row['metadata']),
                    created_at=row['created_at'],
                    parent_checkpoint_id=row['parent_checkpoint_id']
                )
        return None
    
    async def list_checkpoints(
        self, 
        agent_id: str, 
        session_id: str,
        limit: int = 10
    ) -> List[Checkpoint]:
        pool = await self._get_pool()
        async with pool.acquire() as conn:
            rows = await conn.fetch(
                """
                SELECT * FROM checkpoints 
                WHERE agent_id = $1 AND session_id = $2
                ORDER BY created_at DESC
                LIMIT $3
                """,
                agent_id,
                session_id,
                limit
            )
            return [
                Checkpoint(
                    checkpoint_id=row['checkpoint_id'],
                    agent_id=row['agent_id'],
                    session_id=row['session_id'],
                    state=json.loads(row['state']),
                    metadata=json.loads(row['metadata']),
                    created_at=row['created_at'],
                    parent_checkpoint_id=row['parent_checkpoint_id']
                )
                for row in rows
            ]
    
    async def delete(self, checkpoint_id: str) -> None:
        pool = await self._get_pool()
        async with pool.acquire() as conn:
            await conn.execute(
                "DELETE FROM checkpoints WHERE checkpoint_id = $1",
                checkpoint_id
            )


class CheckpointManager:
    """Менеджер checkpoint'ов"""
    
    def __init__(self, store: CheckpointStore):
        self.store = store
    
    async def create_checkpoint(
        self,
        agent_id: str,
        session_id: str,
        state: Dict[str, Any],
        metadata: Dict[str, Any] = None,
        parent_checkpoint_id: Optional[str] = None
    ) -> Checkpoint:
        """Создать новый checkpoint"""
        import uuid
        
        checkpoint = Checkpoint(
            checkpoint_id=str(uuid.uuid4()),
            agent_id=agent_id,
            session_id=session_id,
            state=state,
            metadata=metadata or {},
            parent_checkpoint_id=parent_checkpoint_id
        )
        
        await self.store.save(checkpoint)
        return checkpoint
    
    async def restore_checkpoint(self, checkpoint_id: str) -> Optional[Dict[str, Any]]:
        """Восстановить состояние из checkpoint"""
        checkpoint = await self.store.load(checkpoint_id)
        if checkpoint:
            return checkpoint.state
        return None
    
    async def interrupt(
        self,
        agent_id: str,
        session_id: str,
        state: Dict[str, Any],
        reason: str
    ) -> Checkpoint:
        """Прервать выполнение и сохранить checkpoint"""
        return await self.create_checkpoint(
            agent_id=agent_id,
            session_id=session_id,
            state={**state, "interrupted": True, "interrupt_reason": reason},
            metadata={"interrupted": True, "reason": reason}
        )
    
    async def resume(self, checkpoint_id: str) -> Optional[Dict[str, Any]]:
        """Возобновить выполнение из checkpoint"""
        state = await self.restore_checkpoint(checkpoint_id)
        if state and state.get("interrupted"):
            state["interrupted"] = False
            state["resumed"] = True
        return state
    
    async def rollback(
        self,
        agent_id: str,
        session_id: str,
        steps: int = 1
    ) -> Optional[Dict[str, Any]]:
        """Откатить состояние на N шагов"""
        checkpoints = await self.store.list_checkpoints(
            agent_id=agent_id,
            session_id=session_id,
            limit=steps + 1
        )
        
        if len(checkpoints) <= steps:
            return None
        
        target_checkpoint = checkpoints[steps]
        return target_checkpoint.state
    
    async def branch(
        self,
        checkpoint_id: str,
        branch_name: str
    ) -> Checkpoint:
        """Создать ветку от checkpoint"""
        checkpoint = await self.store.load(checkpoint_id)
        if not checkpoint:
            raise ValueError(f"Checkpoint {checkpoint_id} not found")
        
        import uuid
        branch_checkpoint = Checkpoint(
            checkpoint_id=str(uuid.uuid4()),
            agent_id=checkpoint.agent_id,
            session_id=checkpoint.session_id,
            state=checkpoint.state.copy(),
            metadata={**checkpoint.metadata, "branch": branch_name},
            parent_checkpoint_id=checkpoint.checkpoint_id
        )
        
        await self.store.save(branch_checkpoint)
        return branch_checkpoint


# ═══════════════════════════════════════════════════════════════════════════════
# ## 7. DURABLE EXECUTION V2
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: Temporal, Event Bus patterns
# Зачем: Exactly-once semantics, idempotency, backpressure
# Преимущества: Reliability, recovery, monitoring
# Риски: Complexity, storage overhead

import asyncio
from typing import Callable, Awaitable, Optional, Dict, Any, List
from dataclasses import dataclass, field
from datetime import datetime, timedelta
import hashlib
import json


@dataclass
class DurableEvent:
    """Durable событие с idempotency"""
    event_id: str
    event_type: str
    source: str
    payload: Dict[str, Any]
    idempotency_key: str  # Для deduplication
    correlation_id: Optional[str] = None
    priority: int = 0  # 0=normal, 1=high, 2=critical
    ttl: Optional[timedelta] = None
    created_at: datetime = field(default_factory=datetime.utcnow)
    retry_count: int = 0
    max_retries: int = 3


class DurableEventBusV2:
    """
    Durable Event Bus v2 с ACK/NACK, idempotency, backpressure.
    Вдохновлено Temporal и Kafka.
    """
    
    def __init__(
        self,
        max_queue_size: int = 10000,
        worker_count: int = 3,
        enable_backpressure: bool = True
    ):
        self._queue: asyncio.PriorityQueue = asyncio.PriorityQueue(maxsize=max_queue_size)
        self._dlq: asyncio.Queue = asyncio.Queue()
        self._handlers: Dict[str, List[Callable]] = {}
        self._workers: List[asyncio.Task] = []
        self._processed_events: Dict[str, datetime] = {}  # Idempotency cache
        self._ack_callbacks: Dict[str, Callable] = {}
        self._nack_callbacks: Dict[str, Callable] = {}
        self._running = False
        self._worker_count = worker_count
        self._enable_backpressure = enable_backpressure
        self._metrics = {
            "processed": 0,
            "failed": 0,
            "dlq": 0,
            "duplicates": 0
        }
    
    def subscribe(self, event_type: str, handler: Callable) -> None:
        """Подписаться на события"""
        if event_type not in self._handlers:
            self._handlers[event_type] = []
        self._handlers[event_type].append(handler)
    
    def on_ack(self, event_id: str, callback: Callable) -> None:
        """Регистрация callback для ACK"""
        self._ack_callbacks[event_id] = callback
    
    def on_nack(self, event_id: str, callback: Callable) -> None:
        """Регистрация callback для NACK"""
        self._nack_callbacks[event_id] = callback
    
    async def publish(self, event: DurableEvent) -> bool:
        """Публикация события с backpressure"""
        # Проверка idempotency
        if event.idempotency_key in self._processed_events:
            self._metrics["duplicates"] += 1
            return False  # Duplicate
        
        # Backpressure
        if self._enable_backpressure and self._queue.full():
            raise RuntimeError("Queue is full (backpressure)")
        
        # Приоритет (инвертируем для PriorityQueue)
        priority = -event.priority
        await self._queue.put((priority, event))
        return True
    
    async def _process_event(self, event: DurableEvent) -> bool:
        """Обработка события с retry"""
        # Проверка idempotency
        if event.idempotency_key in self._processed_events:
            self._metrics["duplicates"] += 1
            return True
        
        for attempt in range(event.max_retries):
            try:
                handlers = self._handlers.get(event.event_type, [])
                for handler in handlers:
                    await handler(event)
                
                # ACK
                self._processed_events[event.idempotency_key] = datetime.utcnow()
                self._metrics["processed"] += 1
                
                if event.event_id in self._ack_callbacks:
                    await self._ack_callbacks[event.event_id](event)
                
                return True
                
            except Exception as e:
                event.retry_count = attempt + 1
                if attempt < event.max_retries - 1:
                    delay = min(2 ** attempt, 60)
                    await asyncio.sleep(delay)
        
        # NACK → DLQ
        self._metrics["failed"] += 1
        await self._dlq.put(event)
        self._metrics["dlq"] += 1
        
        if event.event_id in self._nack_callbacks:
            await self._nack_callbacks[event.event_id](event, e)
        
        return False
    
    async def _worker_loop(self) -> None:
        """Worker loop"""
        while self._running:
            try:
                priority, event = await self._queue.get()
                await self._process_event(event)
            except asyncio.CancelledError:
                break
            except Exception as e:
                print(f"Worker error: {e}")
    
    async def start(self) -> None:
        """Запуск bus"""
        self._running = True
        for _ in range(self._worker_count):
            self._workers.append(asyncio.create_task(self._worker_loop()))
    
    async def stop(self) -> None:
        """Остановка bus"""
        self._running = False
        for worker in self._workers:
            worker.cancel()
        await asyncio.gather(*self._workers, return_exceptions=True)
    
    def get_metrics(self) -> Dict[str, int]:
        """Получить метрики"""
        return {
            **self._metrics,
            "queue_size": self._queue.qsize(),
            "dlq_size": self._dlq.qsize()
        }
    
    async def cleanup_idempotency_cache(self, max_age: timedelta = timedelta(hours=1)) -> int:
        """Очистка idempotency cache"""
        now = datetime.utcnow()
        to_remove = [
            key for key, timestamp in self._processed_events.items()
            if now - timestamp > max_age
        ]
        for key in to_remove:
            del self._processed_events[key]
        return len(to_remove)


# ═══════════════════════════════════════════════════════════════════════════════
# ## 8. MEMORY SYSTEM V2
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: Hermes Memory Manager, LangGraph Memory
# Зачем: Разные типы памяти для разных сценариев
# Преимущества: Гибкость, эффективность, контекст
# Риски: Сложность, storage overhead

from abc import ABC, abstractmethod
from typing import List, Dict, Any, Optional
from dataclasses import dataclass, field
from datetime import datetime, timedelta
import asyncio


@dataclass
class MemoryItem:
    """Элемент памяти"""
    id: str
    content: str
    memory_type: str  # short_term, long_term, episodic, semantic, working
    metadata: Dict[str, Any] = field(default_factory=dict)
    created_at: datetime = field(default_factory=datetime.utcnow)
    accessed_at: datetime = field(default_factory=datetime.utcnow)
    access_count: int = 0
    ttl: Optional[timedelta] = None
    importance: float = 0.5  # 0.0 - 1.0


class MemoryStore(ABC):
    """Абстракция для хранилища памяти"""
    
    @abstractmethod
    async def store(self, item: MemoryItem) -> None:
        """Сохранить элемент"""
        pass
    
    @abstractmethod
    async def retrieve(self, query: str, limit: int = 10) -> List[MemoryItem]:
        """Извлечь элементы по запросу"""
        pass
    
    @abstractmethod
    async def delete(self, item_id: str) -> None:
        """Удалить элемент"""
        pass
    
    @abstractmethod
    async def list_items(
        self, 
        memory_type: Optional[str] = None,
        limit: int = 100
    ) -> List[MemoryItem]:
        """Список элементов"""
        pass


class ShortTermMemory:
    """Кратковременная память (рабочая память)"""
    
    def __init__(self, max_size: int = 100, ttl: timedelta = timedelta(minutes=30)):
        self.max_size = max_size
        self.ttl = ttl
        self._items: List[MemoryItem] = []
        self._lock = asyncio.Lock()
    
    async def add(self, content: str, metadata: Dict[str, Any] = None) -> MemoryItem:
        """Добавить в кратковременную память"""
        async with self._lock:
            import uuid
            item = MemoryItem(
                id=str(uuid.uuid4()),
                content=content,
                memory_type="short_term",
                metadata=metadata or {},
                ttl=self.ttl
            )
            
            self._items.append(item)
            
            # Eviction если превышен размер
            if len(self._items) > self.max_size:
                self._items.pop(0)
            
            return item
    
    async def get_recent(self, limit: int = 10) -> List[MemoryItem]:
        """Получить недавние элементы"""
        now = datetime.utcnow()
        valid_items = [
            item for item in self._items
            if item.ttl is None or (now - item.created_at) < item.ttl
        ]
        return valid_items[-limit:]
    
    async def clear(self) -> None:
        """Очистить память"""
        async with self._lock:
            self._items.clear()


class LongTermMemory:
    """Долговременная память"""
    
    def __init__(self, store: MemoryStore):
        self.store = store
    
    async def store_memory(
        self, 
        content: str, 
        metadata: Dict[str, Any] = None,
        importance: float = 0.5
    ) -> MemoryItem:
        """Сохранить в долговременную память"""
        import uuid
        item = MemoryItem(
            id=str(uuid.uuid4()),
            content=content,
            memory_type="long_term",
            metadata=metadata or {},
            importance=importance
        )
        
        await self.store.store(item)
        return item
    
    async def retrieve(self, query: str, limit: int = 10) -> List[MemoryItem]:
        """Извлечь из долговременной памяти"""
        return await self.store.retrieve(query, limit)


class EpisodicMemory:
    """Эпизодическая память (события, опыт)"""
    
    def __init__(self, store: MemoryStore):
        self.store = store
    
    async def record_episode(
        self,
        episode_type: str,
        context: Dict[str, Any],
        outcome: str,
        metadata: Dict[str, Any] = None
    ) -> MemoryItem:
        """Записать эпизод"""
        import uuid
        content = f"Episode: {episode_type}\nContext: {context}\nOutcome: {outcome}"
        
        item = MemoryItem(
            id=str(uuid.uuid4()),
            content=content,
            memory_type="episodic",
            metadata={**(metadata or {}), "episode_type": episode_type, "outcome": outcome}
        )
        
        await self.store.store(item)
        return item
    
    async def retrieve_similar(
        self,
        episode_type: str,
        limit: int = 5
    ) -> List[MemoryItem]:
        """Извлечь похожие эпизоды"""
        items = await self.store.list_items(memory_type="episodic", limit=100)
        filtered = [
            item for item in items
            if item.metadata.get("episode_type") == episode_type
        ]
        return filtered[:limit]


class SemanticMemory:
    """Семантическая память (факты, знания)"""
    
    def __init__(self, store: MemoryStore):
        self.store = store
    
    async def store_fact(
        self,
        subject: str,
        predicate: str,
        object: str,
        metadata: Dict[str, Any] = None
    ) -> MemoryItem:
        """Сохранить факт (триплет)"""
        import uuid
        content = f"{subject} {predicate} {object}"
        
        item = MemoryItem(
            id=str(uuid.uuid4()),
            content=content,
            memory_type="semantic",
            metadata={**(metadata or {}), "subject": subject, "predicate": predicate, "object": object}
        )
        
        await self.store.store(item)
        return item
    
    async def query_facts(
        self,
        subject: Optional[str] = None,
        predicate: Optional[str] = None,
        object: Optional[str] = None
    ) -> List[MemoryItem]:
        """Запросить факты"""
        items = await self.store.list_items(memory_type="semantic", limit=1000)
        
        filtered = items
        if subject:
            filtered = [i for i in filtered if i.metadata.get("subject") == subject]
        if predicate:
            filtered = [i for i in filtered if i.metadata.get("predicate") == predicate]
        if object:
            filtered = [i for i in filtered if i.metadata.get("object") == object]
        
        return filtered


class WorkingMemory:
    """Рабочая память (текущий контекст)"""
    
    def __init__(self, max_size: int = 20):
        self.max_size = max_size
        self._context: Dict[str, Any] = {}
        self._lock = asyncio.Lock()
    
    async def set(self, key: str, value: Any) -> None:
        """Установить значение"""
        async with self._lock:
            self._context[key] = value
            
            # Eviction если превышен размер
            if len(self._context) > self.max_size:
                # Удаляем наименее важное
                oldest_key = next(iter(self._context))
                del self._context[oldest_key]
    
    async def get(self, key: str) -> Optional[Any]:
        """Получить значение"""
        return self._context.get(key)
    
    async def get_all(self) -> Dict[str, Any]:
        """Получить весь контекст"""
        return self._context.copy()
    
    async def clear(self) -> None:
        """Очистить"""
        async with self._lock:
            self._context.clear()


class MemoryManager:
    """Менеджер памяти (объединяет все типы)"""
    
    def __init__(self, store: MemoryStore):
        self.short_term = ShortTermMemory()
        self.long_term = LongTermMemory(store)
        self.episodic = EpisodicMemory(store)
        self.semantic = SemanticMemory(store)
        self.working = WorkingMemory()
    
    async def add_to_short_term(self, content: str, metadata: Dict[str, Any] = None):
        return await self.short_term.add(content, metadata)
    
    async def add_to_long_term(
        self, 
        content: str, 
        metadata: Dict[str, Any] = None,
        importance: float = 0.5
    ):
        return await self.long_term.store_memory(content, metadata, importance)
    
    async def record_episode(
        self,
        episode_type: str,
        context: Dict[str, Any],
        outcome: str
    ):
        return await self.episodic.record_episode(episode_type, context, outcome)
    
    async def store_fact(self, subject: str, predicate: str, object: str):
        return await self.semantic.store_fact(subject, predicate, object)
    
    async def set_working_context(self, key: str, value: Any):
        await self.working.set(key, value)
    
    async def get_context_summary(self) -> Dict[str, Any]:
        """Получить сводку контекста"""
        return {
            "short_term": await self.short_term.get_recent(5),
            "working": await self.working.get_all()
        }


# ═══════════════════════════════════════════════════════════════════════════════
# ## 9. PLUGIN SDK
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: OpenHands Plugin System, Semantic Kernel Plugins
# Зачем: Extensibility, modularity, ecosystem
# Преимущества: Переиспользование, community
# Риски: Security, compatibility

from abc import ABC, abstractmethod
from typing import List, Dict, Any, Optional, Callable
from dataclasses import dataclass, field
from datetime import datetime
import json
import os


@dataclass
class PluginManifest:
    """Манифест плагина"""
    name: str
    version: str
    description: str
    author: str
    dependencies: List[str] = field(default_factory=list)
    permissions: List[str] = field(default_factory=list)  # tool_access, memory_access, etc.
    capabilities: List[str] = field(default_factory=list)  # tools, hooks, skills
    enabled: bool = True
    installed_at: datetime = field(default_factory=datetime.utcnow)


@dataclass
class PluginInfo:
    """Информация об установленном плагине"""
    manifest: PluginManifest
    path: str
    enabled: bool = True


class Plugin(ABC):
    """Базовый класс для плагина"""
    
    @abstractmethod
    def register(self, api: 'PluginAPI') -> None:
        """Регистрация плагина"""
        pass
    
    @abstractmethod
    def get_manifest(self) -> PluginManifest:
        """Получить манифест"""
        pass


class PluginAPI:
    """API для плагинов"""
    
    def __init__(self):
        self._tools: Dict[str, Callable] = {}
        self._hooks: Dict[str, List[Callable]] = {}
        self._skills: List[Dict[str, Any]] = []
    
    def register_tool(self, name: str, handler: Callable, schema: Dict[str, Any]) -> None:
        """Зарегистрировать инструмент"""
        self._tools[name] = {
            "handler": handler,
            "schema": schema
        }
    
    def register_hook(self, hook_name: str, handler: Callable) -> None:
        """Зарегистрировать hook"""
        if hook_name not in self._hooks:
            self._hooks[hook_name] = []
        self._hooks[hook_name].append(handler)
    
    def register_skill(self, skill: Dict[str, Any]) -> None:
        """Зарегистрировать skill"""
        self._skills.append(skill)
    
    def get_tools(self) -> Dict[str, Dict[str, Any]]:
        return self._tools
    
    def get_hooks(self) -> Dict[str, List[Callable]]:
        return self._hooks
    
    def get_skills(self) -> List[Dict[str, Any]]:
        return self._skills


class PluginManager:
    """Менеджер плагинов"""
    
    def __init__(self, plugins_dir: str = "~/.agent/plugins"):
        self.plugins_dir = os.path.expanduser(plugins_dir)
        self._plugins: Dict[str, PluginInfo] = {}
        self._api = PluginAPI()
        
        # Создаём директорию если не существует
        os.makedirs(self.plugins_dir, exist_ok=True)
    
    def install(self, plugin_path: str) -> PluginInfo:
        """Установить плагин"""
        # Загрузка манифеста
        manifest_path = os.path.join(plugin_path, "manifest.json")
        with open(manifest_path, 'r') as f:
            manifest_data = json.load(f)
        
        manifest = PluginManifest(**manifest_data)
        
        # Копирование в plugins_dir
        import shutil
        dest_path = os.path.join(self.plugins_dir, manifest.name)
        if os.path.exists(dest_path):
            shutil.rmtree(dest_path)
        shutil.copytree(plugin_path, dest_path)
        
        # Сохранение info
        info = PluginInfo(manifest=manifest, path=dest_path, enabled=True)
        self._plugins[manifest.name] = info
        
        # Сохранение в installed.json
        self._save_installed()
        
        return info
    
    def load(self, plugin_name: str) -> Plugin:
        """Загрузить плагин"""
        if plugin_name not in self._plugins:
            raise ValueError(f"Plugin {plugin_name} not installed")
        
        info = self._plugins[plugin_name]
        
        # Динамическая загрузка модуля
        import sys
        sys.path.insert(0, info.path)
        
        try:
            import plugin as plugin_module
            plugin_instance = plugin_module.Plugin()
            
            # Регистрация
            plugin_instance.register(self._api)
            
            return plugin_instance
        finally:
            sys.path.remove(info.path)
    
    def enable(self, plugin_name: str) -> None:
        """Включить плагин"""
        if plugin_name not in self._plugins:
            raise ValueError(f"Plugin {plugin_name} not installed")
        
        self._plugins[plugin_name].enabled = True
        self._save_installed()
    
    def disable(self, plugin_name: str) -> None:
        """Выключить плагин"""
        if plugin_name not in self._plugins:
            raise ValueError(f"Plugin {plugin_name} not installed")
        
        self._plugins[plugin_name].enabled = False
        self._save_installed()
    
    def uninstall(self, plugin_name: str) -> None:
        """Удалить плагин"""
        if plugin_name not in self._plugins:
            raise ValueError(f"Plugin {plugin_name} not installed")
        
        info = self._plugins[plugin_name]
        
        # Удаление директории
        import shutil
        if os.path.exists(info.path):
            shutil.rmtree(info.path)
        
        del self._plugins[plugin_name]
        self._save_installed()
    
    def list_installed(self) -> List[PluginInfo]:
        """Список установленных плагинов"""
        return list(self._plugins.values())
    
    def load_all_enabled(self) -> List[Plugin]:
        """Загрузить все включенные плагины"""
        plugins = []
        for name, info in self._plugins.items():
            if info.enabled:
                try:
                    plugin = self.load(name)
                    plugins.append(plugin)
                except Exception as e:
                    print(f"Failed to load plugin {name}: {e}")
        return plugins
    
    def _save_installed(self) -> None:
        """Сохранить список установленных"""
        installed_path = os.path.join(self.plugins_dir, "installed.json")
        data = {
            name: {
                "manifest": {
                    "name": info.manifest.name,
                    "version": info.manifest.version,
                    "description": info.manifest.description,
                    "author": info.manifest.author,
                    "dependencies": info.manifest.dependencies,
                    "permissions": info.manifest.permissions,
                    "capabilities": info.manifest.capabilities,
                    "enabled": info.enabled,
                    "installed_at": info.manifest.installed_at.isoformat()
                },
                "path": info.path,
                "enabled": info.enabled
            }
            for name, info in self._plugins.items()
        }
        
        with open(installed_path, 'w') as f:
            json.dump(data, f, indent=2)
    
    def _load_installed(self) -> None:
        """Загрузить список установленных"""
        installed_path = os.path.join(self.plugins_dir, "installed.json")
        if not os.path.exists(installed_path):
            return
        
        with open(installed_path, 'r') as f:
            data = json.load(f)
        
        for name, info_data in data.items():
            manifest = PluginManifest(**info_data["manifest"])
            self._plugins[name] = PluginInfo(
                manifest=manifest,
                path=info_data["path"],
                enabled=info_data["enabled"]
            )


# ═══════════════════════════════════════════════════════════════════════════════
# ## 10. CAPABILITY DISCOVERY
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: Semantic Kernel
# Зачем: Динамическое определение возможностей
# Преимущества: Flexibility, adaptability
# Риски: Overhead, complexity

from typing import List, Dict, Any, Optional
from dataclasses import dataclass


@dataclass
class Capability:
    """Описание возможности"""
    name: str
    type: str  # tool, skill, policy, model, plugin
    description: str
    schema: Optional[Dict[str, Any]] = None
    metadata: Dict[str, Any] = None


class CapabilityRegistry:
    """Реестр возможностей"""
    
    def __init__(self):
        self._capabilities: Dict[str, Capability] = {}
    
    def register(self, capability: Capability) -> None:
        """Зарегистрировать возможность"""
        self._capabilities[capability.name] = capability
    
    def unregister(self, name: str) -> None:
        """Удалить возможность"""
        self._capabilities.pop(name, None)
    
    def get(self, name: str) -> Optional[Capability]:
        """Получить возможность"""
        return self._capabilities.get(name)
    
    def list_all(self) -> List[Capability]:
        """Список всех возможностей"""
        return list(self._capabilities.values())
    
    def list_by_type(self, capability_type: str) -> List[Capability]:
        """Список по типу"""
        return [c for c in self._capabilities.values() if c.type == capability_type]
    
    def discover(self, query: str) -> List[Capability]:
        """Поиск возможностей"""
        query_lower = query.lower()
        return [
            c for c in self._capabilities.values()
            if query_lower in c.name.lower() or query_lower in c.description.lower()
        ]
    
    def get_capabilities_summary(self) -> Dict[str, Any]:
        """Получить сводку возможностей"""
        return {
            "total": len(self._capabilities),
            "by_type": {
                t: len(self.list_by_type(t))
                for t in set(c.type for c in self._capabilities.values())
            },
            "capabilities": [
                {
                    "name": c.name,
                    "type": c.type,
                    "description": c.description
                }
                for c in self._capabilities.values()
            ]
        }


class CapabilityDiscovery:
    """Сервис обнаружения возможностей"""
    
    def __init__(self, registry: CapabilityRegistry):
        self.registry = registry
    
    async def what_can_i_do(self) -> Dict[str, Any]:
        """Что я умею?"""
        return self.registry.get_capabilities_summary()
    
    async def find_tools(self, query: str) -> List[Capability]:
        """Найти инструменты"""
        tools = self.registry.list_by_type("tool")
        if query:
            tools = [t for t in tools if query.lower() in t.name.lower()]
        return tools
    
    async def find_skills(self, query: str) -> List[Capability]:
        """Найти skills"""
        skills = self.registry.list_by_type("skill")
        if query:
            skills = [s for s in skills if query.lower() in s.name.lower()]
        return skills
    
    async def find_policies(self) -> List[Capability]:
        """Найти политики"""
        return self.registry.list_by_type("policy")
    
    async def find_models(self) -> List[Capability]:
        """Найти модели"""
        return self.registry.list_by_type("model")
    
    async def find_plugins(self) -> List[Capability]:
        """Найти плагины"""
        return self.registry.list_by_type("plugin")


# ═══════════════════════════════════════════════════════════════════════════════
# ## 11. WORKFLOW ENGINE
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: CrewAI Flows, Temporal
# Зачем: Оркестрация сложных workflow
# Преимущества: Declarative, composable, recoverable
# Риски: Complexity, debugging

from typing import List, Dict, Any, Callable, Awaitable, Optional
from dataclasses import dataclass, field
from datetime import datetime, timedelta
import asyncio


@dataclass
class WorkflowStep:
    """Шаг workflow"""
    name: str
    handler: Callable[[Dict[str, Any]], Awaitable[Dict[str, Any]]]
    timeout: Optional[timedelta] = None
    retry_policy: Optional[Dict[str, Any]] = None
    compensation: Optional[Callable[[Dict[str, Any]], Awaitable[None]]] = None


@dataclass
class WorkflowContext:
    """Контекст выполнения workflow"""
    workflow_id: str
    state: Dict[str, Any] = field(default_factory=dict)
    started_at: datetime = field(default_factory=datetime.utcnow)
    completed_steps: List[str] = field(default_factory=list)
    failed_step: Optional[str] = None


class WorkflowEngine:
    """
    Workflow Engine с поддержкой branch, merge, parallel, saga.
    Вдохновлено CrewAI Flows и Temporal.
    """
    
    def __init__(self):
        self._workflows: Dict[str, List[WorkflowStep]] = {}
    
    def register_workflow(self, name: str, steps: List[WorkflowStep]) -> None:
        """Зарегистрировать workflow"""
        self._workflows[name] = steps
    
    async def execute(
        self,
        workflow_name: str,
        initial_state: Dict[str, Any] = None
    ) -> WorkflowContext:
        """Выполнить workflow"""
        if workflow_name not in self._workflows:
            raise ValueError(f"Workflow {workflow_name} not found")
        
        steps = self._workflows[workflow_name]
        context = WorkflowContext(
            workflow_id=f"{workflow_name}_{datetime.utcnow().timestamp()}",
            state=initial_state or {}
        )
        
        for step in steps:
            try:
                # Timeout
                if step.timeout:
                    result = await asyncio.wait_for(
                        step.handler(context.state),
                        timeout=step.timeout.total_seconds()
                    )
                else:
                    result = await step.handler(context.state)
                
                context.state.update(result)
                context.completed_steps.append(step.name)
                
            except asyncio.TimeoutError:
                context.failed_step = step.name
                await self._compensate(context, steps[:steps.index(step)])
                raise TimeoutError(f"Step {step.name} timed out")
            
            except Exception as e:
                context.failed_step = step.name
                await self._compensate(context, steps[:steps.index(step)])
                raise
        
        return context
    
    async def _compensate(
        self,
        context: WorkflowContext,
        completed_steps: List[WorkflowStep]
    ) -> None:
        """Компенсация (Saga pattern)"""
        for step in reversed(completed_steps):
            if step.compensation:
                try:
                    await step.compensation(context.state)
                except Exception as e:
                    print(f"Compensation failed for {step.name}: {e}")
    
    async def execute_parallel(
        self,
        steps: List[WorkflowStep],
        initial_state: Dict[str, Any] = None
    ) -> WorkflowContext:
        """Параллельное выполнение"""
        context = WorkflowContext(
            workflow_id=f"parallel_{datetime.utcnow().timestamp()}",
            state=initial_state or {}
        )
        
        tasks = [step.handler(context.state) for step in steps]
        results = await asyncio.gather(*tasks, return_exceptions=True)
        
        for i, result in enumerate(results):
            if isinstance(result, Exception):
                context.failed_step = steps[i].name
                raise result
            context.state.update(result)
            context.completed_steps.append(steps[i].name)
        
        return context
    
    async def execute_branch(
        self,
        condition: Callable[[Dict[str, Any]], bool],
        true_steps: List[WorkflowStep],
        false_steps: List[WorkflowStep],
        initial_state: Dict[str, Any] = None
    ) -> WorkflowContext:
        """Branching (если/иначе)"""
        context = WorkflowContext(
            workflow_id=f"branch_{datetime.utcnow().timestamp()}",
            state=initial_state or {}
        )
        
        if condition(context.state):
            steps = true_steps
        else:
            steps = false_steps
        
        for step in steps:
            result = await step.handler(context.state)
            context.state.update(result)
            context.completed_steps.append(step.name)
        
        return context


# ═══════════════════════════════════════════════════════════════════════════════
# ## 12. SCHEDULER
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: OpenClaw Cron, Celery
# Зачем: Periodic tasks, delayed execution
# Преимущества: Automation, scheduling
# Риски: Complexity, reliability

from typing import Callable, Awaitable, Optional, Dict, Any
from dataclasses import dataclass, field
from datetime import datetime, timedelta
import asyncio


@dataclass
class ScheduledJob:
    """Запланированная задача"""
    job_id: str
    name: str
    handler: Callable[[], Awaitable[None]]
    schedule: str  # cron expression or interval
    next_run: datetime
    last_run: Optional[datetime] = None
    enabled: bool = True
    metadata: Dict[str, Any] = field(default_factory=dict)


class Scheduler:
    """Планировщик задач"""
    
    def __init__(self):
        self._jobs: Dict[str, ScheduledJob] = {}
        self._running = False
        self._task: Optional[asyncio.Task] = None
    
    def add_cron_job(
        self,
        name: str,
        handler: Callable[[], Awaitable[None]],
        cron_expression: str,
        job_id: Optional[str] = None
    ) -> str:
        """Добавить cron задачу"""
        import uuid
        
        job_id = job_id or str(uuid.uuid4())
        next_run = self._parse_cron(cron_expression)
        
        job = ScheduledJob(
            job_id=job_id,
            name=name,
            handler=handler,
            schedule=cron_expression,
            next_run=next_run
        )
        
        self._jobs[job_id] = job
        return job_id
    
    def add_delayed_job(
        self,
        name: str,
        handler: Callable[[], Awaitable[None]],
        delay: timedelta,
        job_id: Optional[str] = None
    ) -> str:
        """Добавить отложенную задачу"""
        import uuid
        
        job_id = job_id or str(uuid.uuid4())
        next_run = datetime.utcnow() + delay
        
        job = ScheduledJob(
            job_id=job_id,
            name=name,
            handler=handler,
            schedule=f"delay:{delay.total_seconds()}",
            next_run=next_run
        )
        
        self._jobs[job_id] = job
        return job_id
    
    def add_periodic_job(
        self,
        name: str,
        handler: Callable[[], Awaitable[None]],
        interval: timedelta,
        job_id: Optional[str] = None
    ) -> str:
        """Добавить периодическую задачу"""
        import uuid
        
        job_id = job_id or str(uuid.uuid4())
        next_run = datetime.utcnow() + interval
        
        job = ScheduledJob(
            job_id=job_id,
            name=name,
            handler=handler,
            schedule=f"interval:{interval.total_seconds()}",
            next_run=next_run
        )
        
        self._jobs[job_id] = job
        return job_id
    
    def remove_job(self, job_id: str) -> None:
        """Удалить задачу"""
        self._jobs.pop(job_id, None)
    
    def enable_job(self, job_id: str) -> None:
        """Включить задачу"""
        if job_id in self._jobs:
            self._jobs[job_id].enabled = True
    
    def disable_job(self, job_id: str) -> None:
        """Выключить задачу"""
        if job_id in self._jobs:
            self._jobs[job_id].enabled = False
    
    async def start(self) -> None:
        """Запустить планировщик"""
        self._running = True
        self._task = asyncio.create_task(self._scheduler_loop())
    
    async def stop(self) -> None:
        """Остановить планировщик"""
        self._running = False
        if self._task:
            self._task.cancel()
            try:
                await self._task
            except asyncio.CancelledError:
                pass
    
    async def _scheduler_loop(self) -> None:
        """Цикл планировщика"""
        while self._running:
            now = datetime.utcnow()
            
            for job in list(self._jobs.values()):
                if job.enabled and job.next_run <= now:
                    try:
                        await job.handler()
                        job.last_run = now
                        
                        # Обновление next_run
                        if job.schedule.startswith("delay:"):
                            # Одноразовая задача
                            del self._jobs[job.job_id]
                        elif job.schedule.startswith("interval:"):
                            interval = float(job.schedule.split(":")[1])
                            job.next_run = now + timedelta(seconds=interval)
                        else:
                            # Cron
                            job.next_run = self._parse_cron(job.schedule)
                    
                    except Exception as e:
                        print(f"Job {job.name} failed: {e}")
            
            await asyncio.sleep(1)
    
    def _parse_cron(self, cron_expression: str) -> datetime:
        """Парсинг cron expression (упрощенный)"""
        # Упрощенная реализация
        # В production использовать croniter
        return datetime.utcnow() + timedelta(minutes=1)
    
    def list_jobs(self) -> List[ScheduledJob]:
        """Список задач"""
        return list(self._jobs.values())


# ═══════════════════════════════════════════════════════════════════════════════
# ## 13. OBSERVABILITY V2 (OTEL GenAI)
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: OpenTelemetry GenAI Semantic Conventions
# Зачем: Полная observability для LLM систем
# Преимущества: Standardization, monitoring, debugging
# Риски: Overhead, complexity

from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from typing import Dict, Any, Optional
from dataclasses import dataclass, field
from datetime import datetime
import time


@dataclass
class LLMMetrics:
    """Метрики LLM вызова"""
    model: str
    prompt_tokens: int = 0
    completion_tokens: int = 0
    total_tokens: int = 0
    latency_ms: float = 0.0
    cost_usd: float = 0.0
    success: bool = True
    error: Optional[str] = None


class ObservabilityV2:
    """
    Observability v2 с OTEL GenAI semantic conventions.
    """
    
    def __init__(self, otlp_endpoint: str = "http://localhost:4317"):
        # Настройка tracer
        self.tracer_provider = TracerProvider()
        exporter = OTLPSpanExporter(endpoint=otlp_endpoint)
        self.tracer_provider.add_span_processor(BatchSpanProcessor(exporter))
        trace.set_tracer_provider(self.tracer_provider)
        
        self.tracer = trace.get_tracer("agent.system")
        
        # Метрики
        self._metrics: Dict[str, LLMMetrics] = {}
    
    def trace_llm_call(
        self,
        model: str,
        messages: list,
        response: Any,
        usage: Dict[str, int],
        latency_ms: float,
        cost_usd: float = 0.0
    ) -> None:
        """Трассировка LLM вызова"""
        with self.tracer.start_as_current_span("llm.call") as span:
            span.set_attribute("gen_ai.system", "llm")
            span.set_attribute("gen_ai.request.model", model)
            span.set_attribute("gen_ai.request.temperature", 0.7)
            span.set_attribute("gen_ai.usage.prompt_tokens", usage.get("prompt_tokens", 0))
            span.set_attribute("gen_ai.usage.completion_tokens", usage.get("completion_tokens", 0))
            span.set_attribute("gen_ai.usage.total_tokens", usage.get("total_tokens", 0))
            span.set_attribute("gen_ai.response.finish_reasons", ["stop"])
            span.set_attribute("gen_ai.cost.usd", cost_usd)
            span.set_attribute("gen_ai.latency.ms", latency_ms)
            
            # Сохранение метрик
            metrics = LLMMetrics(
                model=model,
                prompt_tokens=usage.get("prompt_tokens", 0),
                completion_tokens=usage.get("completion_tokens", 0),
                total_tokens=usage.get("total_tokens", 0),
                latency_ms=latency_ms,
                cost_usd=cost_usd
            )
            self._metrics[f"llm_{datetime.utcnow().timestamp()}"] = metrics
    
    def trace_tool_call(
        self,
        tool_name: str,
        input_data: Dict[str, Any],
        output_data: Any,
        latency_ms: float
    ) -> None:
        """Трассировка вызова инструмента"""
        with self.tracer.start_as_current_span("tool.call") as span:
            span.set_attribute("tool.name", tool_name)
            span.set_attribute("tool.input", str(input_data))
            span.set_attribute("tool.output", str(output_data)[:1000])  # Truncate
            span.set_attribute("tool.latency.ms", latency_ms)
    
    def trace_agent_step(
        self,
        agent_id: str,
        step_type: str,
        state: Dict[str, Any],
        duration_ms: float
    ) -> None:
        """Трассировка шага агента"""
        with self.tracer.start_as_current_span("agent.step") as span:
            span.set_attribute("agent.id", agent_id)
            span.set_attribute("agent.step.type", step_type)
            span.set_attribute("agent.step.duration.ms", duration_ms)
    
    def get_cost_summary(self) -> Dict[str, Any]:
        """Получить сводку по стоимости"""
        total_cost = sum(m.cost_usd for m in self._metrics.values())
        total_tokens = sum(m.total_tokens for m in self._metrics.values())
        
        return {
            "total_cost_usd": total_cost,
            "total_tokens": total_tokens,
            "total_calls": len(self._metrics),
            "avg_latency_ms": sum(m.latency_ms for m in self._metrics.values()) / len(self._metrics) if self._metrics else 0
        }
    
    def get_token_summary(self) -> Dict[str, Any]:
        """Получить сводку по токенам"""
        return {
            "prompt_tokens": sum(m.prompt_tokens for m in self._metrics.values()),
            "completion_tokens": sum(m.completion_tokens for m in self._metrics.values()),
            "total_tokens": sum(m.total_tokens for m in self._metrics.values())
        }


# ═══════════════════════════════════════════════════════════════════════════════
# ## 14. SANDBOX SYSTEM
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: OpenHands Runtime
# Зачем: Безопасное выполнение кода
# Преимущества: Isolation, security, resource limits
# Риски: Complexity, overhead

from abc import ABC, abstractmethod
from typing import Dict, Any, Optional
from dataclasses import dataclass
import subprocess
import asyncio


@dataclass
class ResourceLimits:
    """Лимиты ресурсов"""
    cpu_cores: float = 1.0
    memory_mb: int = 512
    disk_mb: int = 1024
    network_enabled: bool = False
    timeout_seconds: int = 60


@dataclass
class ExecutionResult:
    """Результат выполнения"""
    success: bool
    stdout: str
    stderr: str
    exit_code: int
    duration_ms: float


class Sandbox(ABC):
    """Абстракция sandbox"""
    
    @abstractmethod
    async def execute(
        self,
        command: str,
        limits: ResourceLimits = None
    ) -> ExecutionResult:
        """Выполнить команду"""
        pass
    
    @abstractmethod
    async def upload_file(self, path: str, content: bytes) -> None:
        """Загрузить файл"""
        pass
    
    @abstractmethod
    async def download_file(self, path: str) -> bytes:
        """Скачать файл"""
        pass


class DockerSandbox(Sandbox):
    """Docker-based sandbox"""
    
    def __init__(self, image: str = "python:3.11-slim"):
        self.image = image
        self.container_id: Optional[str] = None
    
    async def start(self) -> None:
        """Запустить контейнер"""
        proc = await asyncio.create_subprocess_exec(
            "docker", "run", "-d", "--rm",
            "--memory=512m",
            "--cpus=1.0",
            "--network=none",
            self.image,
            "sleep", "infinity",
            stdout=asyncio.subprocess.PIPE,
            stderr=asyncio.subprocess.PIPE
        )
        stdout, _ = await proc.communicate()
        self.container_id = stdout.decode().strip()
    
    async def stop(self) -> None:
        """Остановить контейнер"""
        if self.container_id:
            await asyncio.create_subprocess_exec(
                "docker", "stop", self.container_id
            )
    
    async def execute(
        self,
        command: str,
        limits: ResourceLimits = None
    ) -> ExecutionResult:
        """Выполнить команду в контейнере"""
        if not self.container_id:
            await self.start()
        
        start_time = asyncio.get_event_loop().time()
        
        proc = await asyncio.create_subprocess_exec(
            "docker", "exec", self.container_id,
            "sh", "-c", command,
            stdout=asyncio.subprocess.PIPE,
            stderr=asyncio.subprocess.PIPE
        )
        
        try:
            stdout, stderr = await asyncio.wait_for(
                proc.communicate(),
                timeout=limits.timeout_seconds if limits else 60
            )
            
            duration_ms = (asyncio.get_event_loop().time() - start_time) * 1000
            
            return ExecutionResult(
                success=proc.returncode == 0,
                stdout=stdout.decode(),
                stderr=stderr.decode(),
                exit_code=proc.returncode,
                duration_ms=duration_ms
            )
        
        except asyncio.TimeoutError:
            proc.kill()
            return ExecutionResult(
                success=False,
                stdout="",
                stderr="Timeout",
                exit_code=-1,
                duration_ms=limits.timeout_seconds * 1000 if limits else 60000
            )
    
    async def upload_file(self, path: str, content: bytes) -> None:
        """Загрузить файл в контейнер"""
        # Упрощенная реализация
        pass
    
    async def download_file(self, path: str) -> bytes:
        """Скачать файл из контейнера"""
        # Упрощенная реализация
        return b""


class ProcessSandbox(Sandbox):
    """Process-based sandbox (для тестирования)"""
    
    async def execute(
        self,
        command: str,
        limits: ResourceLimits = None
    ) -> ExecutionResult:
        """Выполнить команду"""
        start_time = asyncio.get_event_loop().time()
        
        proc = await asyncio.create_subprocess_shell(
            command,
            stdout=asyncio.subprocess.PIPE,
            stderr=asyncio.subprocess.PIPE
        )
        
        try:
            stdout, stderr = await asyncio.wait_for(
                proc.communicate(),
                timeout=limits.timeout_seconds if limits else 60
            )
            
            duration_ms = (asyncio.get_event_loop().time() - start_time) * 1000
            
            return ExecutionResult(
                success=proc.returncode == 0,
                stdout=stdout.decode(),
                stderr=stderr.decode(),
                exit_code=proc.returncode,
                duration_ms=duration_ms
            )
        
        except asyncio.TimeoutError:
            proc.kill()
            return ExecutionResult(
                success=False,
                stdout="",
                stderr="Timeout",
                exit_code=-1,
                duration_ms=limits.timeout_seconds * 1000 if limits else 60000
            )
    
    async def upload_file(self, path: str, content: bytes) -> None:
        with open(path, 'wb') as f:
            f.write(content)
    
    async def download_file(self, path: str) -> bytes:
        with open(path, 'rb') as f:
            return f.read()


# ═══════════════════════════════════════════════════════════════════════════════
# ## 15. MODEL ROUTER
# ═══════════════════════════════════════════════════════════════════════════════

# Источник: MTRouter, Cost-Aware Routing
# Зачем: Оптимальный выбор модели
# Преимущества: Cost optimization, performance
# Риски: Complexity, routing errors

from typing import List, Dict, Any, Optional
from dataclasses import dataclass
from enum import Enum


class ModelTier(Enum):
    """Уровень модели"""
    SLM = "slm"  # Small Language Model
    LLM = "llm"  # Large Language Model
    REASONING = "reasoning"  # Reasoning Model
    CODE = "code"  # Code Model
    VISION = "vision"  # Vision Model


@dataclass
class ModelConfig:
    """Конфигурация модели"""
    name: str
    tier: ModelTier
    cost_per_1k_tokens: float
    avg_latency_ms: float
    max_context_length: int
    capabilities: List[str]


@dataclass
class RoutingDecision:
    """Решение о маршрутизации"""
    model: str
    reason: str
    estimated_cost: float
    estimated_latency_ms: float


class ModelRouter:
    """
    Model Router с cost-aware и latency-aware routing.
    """
    
    def __init__(self):
        self._models: Dict[str, ModelConfig] = {}
        self._routing_policies: Dict[str, callable] = {}
    
    def register_model(self, config: ModelConfig) -> None:
        """Зарегистрировать модель"""
        self._models[config.name] = config
    
    def register_policy(self, name: str, policy: callable) -> None:
        """Зарегистрировать политику маршрутизации"""
        self._routing_policies[name] = policy
    
    async def route(
        self,
        task: str,
        context: Dict[str, Any] = None,
        policy: str = "cost_aware"
    ) -> RoutingDecision:
        """Маршрутизация запроса"""
        context = context or {}
        
        if policy in self._routing_policies:
            return await self._routing_policies[policy](task, context, self._models)
        
        # Default: cost-aware routing
        return await self._cost_aware_routing(task, context)
    
    async def _cost_aware_routing(
        self,
        task: str,
        context: Dict[str, Any]
    ) -> RoutingDecision:
        """Cost-aware routing"""
        # Определение требуемых capabilities
        required_caps = self._infer_capabilities(task)
        
        # Фильтрация моделей
        suitable_models = [
            m for m in self._models.values()
            if all(cap in m.capabilities for cap in required_caps)
        ]
        
        if not suitable_models:
            # Fallback на самую мощную модель
            suitable_models = list(self._models.values())
        
        # Выбор самой дешевой
        best_model = min(suitable_models, key=lambda m: m.cost_per_1k_tokens)
        
        return RoutingDecision(
            model=best_model.name,
            reason=f"Cheapest model with required capabilities",
            estimated_cost=best_model.cost_per_1k_tokens,
            estimated_latency_ms=best_model.avg_latency_ms
        )
    
    async def _latency_aware_routing(
        self,
        task: str,
        context: Dict[str, Any]
    ) -> RoutingDecision:
        """Latency-aware routing"""
        required_caps = self._infer_capabilities(task)
        
        suitable_models = [
            m for m in self._models.values()
            if all(cap in m.capabilities for cap in required_caps)
        ]
        
        if not suitable_models:
            suitable_models = list(self._models.values())
        
        best_model = min(suitable_models, key=lambda m: m.avg_latency_ms)
        
        return RoutingDecision(
            model=best_model.name,
            reason=f"Fastest model with required capabilities",
            estimated_cost=best_model.cost_per_1k_tokens,
            estimated_latency_ms=best_model.avg_latency_ms
        )
    
    def _infer_capability
