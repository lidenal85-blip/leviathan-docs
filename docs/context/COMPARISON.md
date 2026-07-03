# СРАВНЕНИЕ АГЕНТНЫХ ФРЕЙМВОРКОВ vs МАНИФЕСТ 5.0.0
**Дата:** 2026-07-01 | **Источник:** реальный код из GitHub

## Легенда
✅ Полная реализация | ⚠️ Частичная | ❌ Отсутствует | 🔶 Иначе реализовано

| Область | LangGraph | LangChain | CrewAI | OpenHands | AutoGen | PydanticAI | Semantic Kernel | LlamaIndex | **Манифест 5.0.0** |
|---------|-----------|-----------|--------|-----------|---------|------------|-----------------|------------|--------------------|
| **1. Agent Lifecycle (FSM)** | ✅ StateGraph + Pregel | ❌ | ✅ Flow decorators | ✅ AgentController | ⚠️ BaseChatAgent | ❌ | ❌ | ✅ BaseWorkflowAgent | ✅ AgentLifecycle |
| **2. Runnable Interface** | ✅ Runnable+RunnableConfig | ✅ LCEL полный | ❌ | ❌ | ⚠️ on_messages/stream | ❌ | ⚠️ invoke_stream | ⚠️ Workflow+step | ✅ Runnable+Sequence+Parallel |
| **3. Middleware Pipeline** | ✅ before/after model hooks | ✅ AgentMiddleware v1 | ❌ | ❌ | ❌ | ❌ | ✅ KernelFilterExtension | ❌ | ✅ MiddlewarePipeline |
| **4. Callback System** | ✅ StreamMode+events | ✅ BaseCallbackHandler | ⚠️ @listen decorator | ❌ | ⚠️ CancellationToken | ❌ | ✅ FilterTypes (3 типа) | ⚠️ WorkflowHandler | ✅ CallbackManager (12 hooks) |
| **5. Typed State (Pydantic v2)** | ✅ TypedDict+Annotated+reducers | ⚠️ | ✅ Pydantic BaseModel | ❌ dict | ✅ BaseState Pydantic | ✅ полный | ⚠️ KernelArguments | ✅ AgentContext Pydantic | ✅ TypedState Pydantic v2 |
| **6. Checkpointing** | ✅ InMemory+Postgres+Redis+branch | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ CheckpointStore multi-backend |
| **7. Durable Execution** | ✅ Durability(sync/async/exit)+ACK | ❌ | ❌ | ❌ | ⚠️ CancellationToken | ❌ | ❌ | ❌ | ✅ ACK/NACK+idempotency+DLQ |
| **8. Memory System** | ⚠️ BaseStore only | ⚠️ ConversationBuffer | ❌ | ❌ | ⚠️ ChatHistory | ❌ | ⚠️ ChatHistory | ✅ ChatMemoryBuffer+retrieval | ✅ Short/Long/Episodic/Semantic |
| **9. Plugin SDK** | ❌ | ❌ | ❌ | ✅ Plugin lifecycle full | ❌ | ❌ | ✅ KernelPlugin+discovery | ❌ | ✅ install/load/enable/uninstall |
| **10. Capability Discovery** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ полный | ❌ | ✅ CapabilityRegistry |
| **11. Workflow Engine** | ✅ Branch+Merge+Parallel+Send | ⚠️ LCEL parallel | ✅ @start/@listen/@router | ❌ | ⚠️ Swarm/RoundRobin | ❌ | ❌ | ✅ step+events+parallel | ✅ Branch/Merge/Saga/Human |
| **12. Scheduler** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ Cron+Delayed+Recurring |
| **13. Observability (OTEL)** | ✅ TaskPayload+debug stream | ⚠️ callbacks only | ❌ | ⚠️ | ✅ trace_create_agent_span | ❌ | ⚠️ | ❌ | ✅ OTEL GenAI semantic conventions |
| **14. Sandbox System** | ❌ | ❌ | ❌ | ✅ Docker+Process+Limits | ❌ | ❌ | ❌ | ❌ | ✅ DockerSandbox+ResourceLimits |
| **15. Model Router** | ⚠️ RunnableConfig | ❌ | ❌ | ❌ | ⚠️ | ❌ | ✅ AIServiceSelector | ❌ | ✅ Cost/Latency/Fallback Router |
| **16. Multi-Agent Coordination** | ✅ Subgraphs+Send | ❌ | ✅ Crew+hierarchical | ❌ | ✅ Swarm+RoundRobin+Selector | ❌ | ❌ | ✅ can_handoff_to | ✅ Coordinator+Arbiter |
| **17. Security (Prompt Injection)** | ❌ | ❌ | ❌ | ⚠️ | ❌ | ❌ | ❌ | ❌ | ✅ NFKC+Firewall+PII+RBAC |
| **18. Self-Reflection** | ❌ | ❌ | ⚠️ reflection crews | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ Critic+Evaluator |
| **19. Governance** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ Audit+Compliance+Risk |
| **20. Enterprise HA** | ⚠️ stateless nodes | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ HA+Scaling+DR |

## Счёт (✅ = 1, ⚠️ = 0.5, ❌ = 0)
| Фреймворк | Балл / 20 |
|-----------|----------|
| **Манифест 5.0.0** | **20/20** |
| LangGraph | 11.5/20 |
| LangChain | 5.5/20 |
| CrewAI | 6/20 |
| AutoGen | 5.5/20 |
| LlamaIndex | 5/20 |
| Semantic Kernel | 5/20 |
| OpenHands | 3.5/20 |
| PydanticAI | 2/20 |
