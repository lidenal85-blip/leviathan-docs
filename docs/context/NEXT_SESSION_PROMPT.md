# 🚀 СТАРТ НОВОЙ СЕССИИ — Leviathan / ADR-002
> Этот файл — точка входа для новой сессии с нуля. Прочитай весь файл перед тем как что-либо делать.
> Обновлён: 2026-07-02

---

## ⚡ БЫСТРЫЙ СТАРТ (если времени мало)

```bash
cat /root/analysis/SESSION_CONTEXT_2026-07-02.md   # что было сделано за весь день 1–2 июля
cat /root/analysis/ADR-002/ADR-002-FINAL.md        # что делать дальше (архитектура)
cat /root/analysis/ADR-002/BUFFY-AUDIT-PROMPT.md   # готовый execution mandate для Фазы 1
```
→ Затем выполни “Проверка живого состояния” ниже — там всё, чтобы не повторить уже сделанное.

---

## 🌐 Живой сайт документации (альтернатива)

**https://lidenal85-blip.github.io/leviathan-docs/**

Сайт рабочий (проверено 2026-07-02, HTTP 200), содержит 24 документа из `/root/analysis/`, включая `SESSION_CONTEXT_2026-07-02.md`.
⚠️ **На сайте НЕТ папки `ADR-002/`** — сам мандат на выполнение читать только с сервера напрямую (пути выше). Если хочешь видеть и его на сайте — скажи следующей сессии скопировать `ADR-002/*` в `/root/leviathan-docs/docs/context/adr-002/` и запушить — это не сделано сейчас сознательно, чтобы не делать лишнего без запроса.

GitHub-репо: `https://github.com/lidenal85-blip/leviathan-docs` (ветка `main`, deploy через `.github/workflows/docs.yml`, Pages включён в режиме `build_type: workflow`).

---

## 📌 Кто ты и что происходит

Ты — Claude Code на сервере LEVIATHAN (78.17.24.96), работаешь над `/opt/leviathan_engine/agent_service` — ядром AI-оркестратора Leviathan. За 1–2 июля была реализована Manifest 5.1.0 и утверждён ADR-002 — переход с pipeline-агента на runtime decision-making kernel. Аудит инфраструктуры перед стартом Фазы 1 пройден и выдал “READY”. Кода Фазы 1 ещё нет.

---

## 🔍 Проверка живого состояния (выполни ОБЯЗАТЕЛЬНО перед любой работой)

Состояние меняется быстрее чем обновляются эти заметки — не доверяй тексту, проверь живьём:

```bash
# 1. Где мы по code
cd /opt/leviathan_engine/agent_service
git log --oneline -5
git status --short
git branch --show-current   # ожидается feature/claude-multi-account

# 2. Началась ли уже Фаза 1 ADR-002? (когда-то до тебя могли начать без чекпоинта)
ls -la core/scenarios/ 2>&1
ls -la core/agent/kernel.py core/agent/decision_engine.py core/agent/execution_loop.py 2>&1

# 3. Сервисы живы?
systemctl is-active leviathan_agent den4ik-claude leviathan_mcp_server
curl -s http://127.0.0.1:8400/health   # Secrets Vault
curl -s http://127.0.0.1:8083/health   # Gemini Proxy (если есть health)
```

Если `core/scenarios/loader.py` уже существует — ЗНАЧИТ, КТО-ТО НАЧАЛ ФАЗУ 1 ПОСЛЕ этого файла — читай его первым, не переписывай с нуля.

---

## ✅ Что точно сделано (не переделывать)

- Manifest 5.1.0: StateMachine, Runnable, Middleware — в `agent/core.py`
- Callback System + Workflow Engine + Memory V2
- LongTermMemory на SQLite (`db/long_term_memory.db`), `asyncio.to_thread()`
- Всё это запушено в коммите `f1e3e6a`, ветка `feature/claude-multi-account`
- Buffy-аудит инфраструктуры пройден, вывод: READY FOR ADR-002 PHASE 1
- Сайт документации `leviathan-docs` починен и задеплоен (была отдельная задача про Pages/submodules — закрыта, см. ниже)

---

## ⚠️ Незакрытые хвосты

1. **`db/memory_system.py`** — однострочный незакоммиченный фикс (второй `sqlite3.connect()` без `check_same_thread=False`). `git diff db/memory_system.py` чтобы проверить, закоммитить или откатить.
2. **`feature/claude-multi-account` → `main`** — не решено, мержить ли. Спроси у Дениса перед тем как мержить.
3. **ADR-002 Фаза 1** не начата. Первый файл к созданию: `core/scenarios/loader.py`, класс `ScenarioPolicy` (Pydantic). Полный план — в `ADR-002-FINAL.md`.
4. Сайт `leviathan-docs`: папка `ADR-002/` не синхронизирована туда (см. выше). Также лишние черновики (`MANIFEST_5.0.0.md`, возможно устаревший `TASK_PROMPT.md`) попали туда же — можно почистить навигацию, если попросят.

---

## 🎯 Рекомендуемый первый шаг

Если Денис не дал иной конкретный приоритет, по умолчанию:
1. Закрыть мелкие хвосты выше (коммит memory_system.py, уточнить merge-вопрос)
2. Прочитать `BUFFY-AUDIT-PROMPT.md` полностью — там готовый execution mandate для Фазы 1
3. Начать Фазу 1 ADR-002: создать `core/scenarios/loader.py` по спецификации из `ADR-002-FINAL.md`

---
*Источники: `/root/analysis/SESSION_CONTEXT_2026-07-02.md`, `/root/analysis/ADR-002/*`, живая проверка git+systemctl+GitHub API на момент написания.*