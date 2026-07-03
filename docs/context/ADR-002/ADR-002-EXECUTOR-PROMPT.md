# 🚀 ADR-002 IMPLEMENTATION EXECUTOR PROMPT

**READY FOR AGENT SUBMISSION**

## ЦЕЛЬ

Реализовать **Dynamic Scenario-Driven Agent Execution Engine** в LEVIATHAN за 17 дней.
Превратить pipeline-модель в runtime decision-making kernel.

## ЧТО НУЖНО СДЕЛАТЬ

### ФАЗА 1: CORE KERNEL (3 дня) ← НАЧНИ ОТСЮДА

Создать 4 компонента (2000 строк кода):

1. **ScenarioPolicy Loader** (`core/scenarios/loader.py`)
   - Парсить YAML политики
   - Валидировать схему
   - Hot-reload поддержка

2. **AgentKernel** (`core/agent/kernel.py`)
   - Protocol интерфейс
   - RuntimeAgentKernel имплементация
   - Интеграция с LeviathanAgent

3. **DecisionEngine** (`core/agent/decision_engine.py`)
   - LLM-based ranking (top-3)
   - Cost estimation
   - Adaptive scoring formula

4. **ExecutionLoop** (`core/agent/execution_loop.py`)
   - Основной цикл (observe → decide → act → eval)
   - Checkpoint на каждом шаге
   - Integration с EventBus

**Результат фазы 1:**
- ✅ Динамичное планирование (не pipeline)
- ✅ CLI `lev-cli run scenario --mode PRO` работает
- ✅ JSONL логирование
- ✅ 80%+ тестов

### ФАЗА 2: ROLES & SAFETY (4 дня)

Создать 4 компонента:
1. RoleRegistry
2. SafetyGuards (7 механизмов)
3. CostManager
4. LoopDetector

### ФАЗА 3: CLI & OBSERVABILITY (2 дня)

Расширить CLI, реализовать трассирование

### ФАЗА 4: INTEGRATION & TESTS (3 дня)

Unit/Integration/E2E тесты (80%+ покрытие)

### ФАЗА 5: DOCS & MVP (2 дня)

Документация, примеры, демо

## ОБЯЗАТЕЛЬНО

- ✅ Работай БЕЗ остановок — нет "продолжить?"
- ✅ Логируй ВСЕ действия в `/root/implementation_log.md`
- ✅ Создавай отчёт после каждой фазы
- ✅ При ошибках — исправляй и идёшь дальше
- ✅ Checkpoint + rollback для всех изменений

## ФАЙЛЫ, КОТОРЫЕ УЖЕ ЕСТЬ

- `event_bus.py` ✅ (используй для публикации событий)
- `task_manager.py` ✅ (используй для хранения)
- `ContextKeeper` ✅ (используй для checkpointing)
- Providers (Gemini, Groq, DeepSeek) ✅
- Modes (AUTO/FLASH/PRO) ✅

## РЕЗУЛЬТАТ

После выполнения:
- 17 новых файлов в `/opt/leviathan_engine/agent_service/`
- 5000+ строк кода
- 80%+ test coverage
- Полная документация
- Готовность к production

## НАЧНИ НЕМЕДЛЕННО

1. Прочитай `/root/analysis/ADR-002/ADR-002-FINAL.md`
2. Начни с ФАЗЫ 1 (Core Kernel)
3. Каждый день — checkpoint + отчёт
4. После каждой фазы — полный review

**ВПЕРЁД! РЕАЛИЗУЙ ADR-002!**
