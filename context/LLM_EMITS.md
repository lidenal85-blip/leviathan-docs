# 🔔 LLM эмиты — Отчёт

**Дата:** 2026-07-01
**Компонент:** Manifest 5.1.0 — Callback System

## Что добавлено

### 1. LLM_START (core.py — _run_gemini_loop)
- Перед middleware before_model
- Данные: `messages_count`, `model`

### 2. LLM_END (core.py — _run_gemini_loop)
- После успешного provider.call()
- Данные: `ok=True`, `has_tool_calls`

### 3. LLM_ERROR (core.py — _run_gemini_loop)
- При ошибке provider.call()
- Данные: `error` message

### 4. STATE_TRANSITION (state.py — StateMachine.transition())
- При каждом переходе состояния
- Данные: `from`, `to`, `metadata`
- Через `set_callback_manager()` — без circular imports

### 5. AGENT_START (core.py — run())
- При старте задачи

### 6. TOOL_START / TOOL_END (core.py — _execute_tool())
- При вызове и завершении инструмента

## Интеграция
- CallbackManager создаётся в `LeviathanAgent.__init__()`
- Подключается к legacy EventBus через `connect_legacy_bus()`
- StateMachine получает ссылку через `set_callback_manager()`
