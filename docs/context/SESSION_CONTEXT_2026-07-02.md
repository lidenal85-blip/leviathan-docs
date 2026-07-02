# FreeBuff / Leviathan — Контекст последней сессии
**Свод от:** 2026-07-02
**Охват:** вся работа 1 июля (07:33–17:03) + продолжение в ночь на 2 июля (23:37–00:07)
**Источник:** 20 tmux-сессий (`freebuff-0..6`, `freeb-7..23`) + `/root/analysis/*.md`

---

## TL;DR

Один длинный рабочий день (5 из 5 доступных Claude Code сессий использовано) ушёл на:
1. Аудит и сравнение архитектуры Leviathan CLI с LangGraph/CrewAI/OpenHands (утро)
2. Реализацию Manifest 5.1.0 — StateMachine, Callbacks, Workflow Engine, Memory V2, SQLite-персистентность (день)
3. Ночью (уже после формального конца дня) — новый архитектурный виток: **ADR-002**, решение о переходе с pipeline-агента на **runtime decision-making kernel**

Система сейчас в состоянии "аудит пройден, ждёт исполнителя для Фазы 1 ADR-002". Один незакоммиченный однострочный фикс в git.

---

## Хронология

| Время | Событие | Файл-источник |
|---|---|---|
| 06:02 | Написан манифест 5.0.0 — архитектурный аудит | `MANIFEST_5.0.0.md` (104K) |
| 06:28 | Сравнение с конкурентами (LangGraph и др.) | `COMPARISON.md` |
| 07:55 | Системный анализ (свой код) | `MY_SYSTEM_ANALYSIS.md` (37K) |
| 08:21 | Составлен TASK_PROMPT для следующего шага | `TASK_PROMPT.md` (35K) |
| 08:30–08:47 | Аудит секретов + план миграции манифеста | `SECRETS_ANALYSIS.md`, `MANIFEST_MIGRATION_PLAN.md` |
| 09:43 | План выполнения (P1/P2/P3) | `EXECUTION_PLAN.md` |
| 10:43 | Собран полный анализ (189K) — база знаний на весь день | `ALL_ANALYSIS.md` |
| 14:05 | Чекпоинт восстановления сессии (после лимита) | `restore.md` |
| Шаг P1 | StateMachine + Runnable + Middleware вшиты в core.py | коммит `4c25966` |
| Шаг P2 | Callback System + Workflow Engine + Memory V2 | коммит `4c25966`/`f1e3e6a` |
| Шаг P3 | GitHub sync всех проектов | `P3_GITHUB_SYNC.md` |
| 16:46 | LLM emits задокументированы | `LLM_EMITS.md` |
| Шаг 3 | LongTermMemory переведена на SQLite (`db/long_term_memory.db`) | `MEMORY_SQLITE.md` |
| 16:50 | Финальный отчёт Manifest 5.1.0 (138 строк) | `MANIFEST_5.1_FINAL_REPORT.md` |
| 16:50 | Анализ логов → найдена версия Freebuff v3.5 (AES-256-KW, signed checkpoints, ThreadPool 32) | `LOGS_ANALYSIS.md` |
| **17:03** | **Коммит и пуш** `f1e3e6a` в `feature/claude-multi-account`, все 6 smoke-тестов ✅ | конец `freeb-23`, "5 of 5 sessions used today" |
| 23:37–23:44 | **Новая ветка работы (уже 2 июля по факту):** buffy-аудит инфраструктуры перед стартом нового этапа | `buffy-audit-1782949444.txt` |
| 23:42–23:57 | Написан и утверждён **ADR-002** (2.0 FINAL, синтез v1+v2) | `ADR-002-EXECUTOR-PROMPT.md`, `ADR-002-FINAL.md` (33K) |
| **00:07 (2 июля)** | Финальный промпт для агента-исполнителя ADR-002, аудит подтверждает готовность | `BUFFY-AUDIT-PROMPT.md` |

---

## Что реализовано технически (Manifest 5.1.0)

- **StateMachine + Runnable + Middleware** — вшиты напрямую в `agent/core.py`
- **Callback System + Workflow Engine + Memory V2** — второй слой поверх ядра
- **LongTermMemory на SQLite** (`db/long_term_memory.db`), `check_same_thread=False` + `asyncio.to_thread()`, индексы по `importance DESC, created_at DESC`
- **Secrets Vault** интегрирован (порт 8400, `curl 127.0.0.1:8400/health`)
- **Gemini Proxy** на порту 8083
- Все изменения запушены в ветку `feature/claude-multi-account` (не в `main`)

**Git-состояние сейчас** (`/opt/leviathan_engine/agent_service`):
- HEAD = `f1e3e6a`, ветка `feature/claude-multi-account`, синхронна с origin (0 ahead/behind)
- ⚠️ Один незакоммиченный фикс: `db/memory_system.py` — второй вызов `sqlite3.connect()` не получил `check_same_thread=False` в прошлом коммите. Однострочная правка, не запушена.

---

## ADR-002 — новый архитектурный виток (главное, что нужно знать дальше)

**Статус:** APPROVED, аудит инфраструктуры пройден, ждёт исполнителя.

### Идея
Перевести Leviathan CLI из **pipeline-агента** (`Task → Step1 → Step2 → ... → Result`, жёсткая последовательность) в **runtime decision-making kernel**:
- Сценарий = **Policy** (правила поведения), а не Pipeline (список шагов)
- Агент сам планирует, выбирает роли, инструменты, **перепланирует** при неудаче
- Cost-aware (бюджет на LLM), observability (каждое решение логируется), safety guards (защита от зацикливания/перерасхода)

### Ключевые компоненты
- `ScenarioPolicy` — YAML-контракт: `intent`, `roles_available`, `decision_policy` (adaptive/strict/cost_optimized), `constraints` (max_steps, max_replans, max_cost_usd, max_execution_time_minutes)
- `AgentKernel` — 5 подсистем: Decision Engine, Role Selector, Execution Loop, Replanning Engine, Safety Guards
- Output contract: `artifacts/state.json`, `reasoning_trace.json`, `execution_log.jsonl`, `final_output.json`

### План: 5 фаз, 17 дней
**Фаза 1 (3 дня) — Core Kernel**, первый файл к созданию:
`/opt/leviathan_engine/agent_service/core/scenarios/loader.py` — класс `ScenarioPolicy` (Pydantic)

### Инфраструктура подтверждена готовой (buffy-audit, 2026-07-02)
| Метрика | Значение |
|---|---|
| Python-файлов | 2,692 |
| Всего строк кода | 412,775 |
| Активных сервисов | 10+ (den4ik-claude, leviathan-daemon, leviathan-hq, leviathan_agent, leviathan_mcp, savdabot, sport-observer, voicestudio, djvu2fb2_web, qemu-guest-agent) |
| Диск свободно | 6.1 GB (79% used) |
| Открытых портов | 30+ |
| **Вывод аудита** | **READY FOR ADR-002 PHASE 1: YES** |

Полный текст решения: `/root/analysis/ADR-002/ADR-002-FINAL.md` (33K, есть YAML-примеры контракта и полная 5-фазная разбивка).
Промпт для передачи исполнителю: `/root/analysis/ADR-002/BUFFY-AUDIT-PROMPT.md`.

---

## Расхождение в наименовании: freebuff vs freeb

Оба имена встречаются на сервере как один и тот же проект в разное время:
- `/usr/bin/freebuff`, `/root/freebuff/`, `freebuffd.service`, `freebuff_v35_install.sh` — установленный продукт
- tmux-группы `freebuff-*` (утро) → `freeb-*` (день) — похоже на сокращение имени сессии в процессе работы, не смена проекта

`LOGS_ANALYSIS.md` (16:50) явно называет проанализированную версию **Freebuff v3.5** с фичами AES-256-KW и signed checkpoints — это про systemd-сервис `freebuffd`, отдельный от `leviathan_engine/agent_service`, над которым шла основная дневная работа. Не путать одно с другим при дальнейшей работе.

---

## Незакрытые вопросы для следующей сессии

1. **Закоммитить и запушить** правку `db/memory_system.py` (или откатить, если она была экспериментальной)
2. **Решить: `feature/claude-multi-account` → `main`?** Ветка не смёржена, 5.1.0 живёт отдельно от main
3. **ADR-002 Фаза 1** не начата фактически — только спланирована и зааудичена. Первый файл: `core/scenarios/loader.py`
4. Уточнить у Дениса: приоритет — доделывать ADR-002 (17 дней, сложность 7/10) или что-то более срочное параллельно (freebuff v3.5 продакшн, diet_platform и т.д.)
5. Полный архив анализа за день — 17 markdown-файлов в `/root/analysis/` (228K только в `ALL_ANALYSIS.md`) — можно почистить старые черновики (`MANIFEST_5.0.0.md` заменён на 5.1, `TASK_PROMPT.md` вероятно устарел) если место на диске понадобится (сейчас 79% занято).

---

*Составлено сведением 20 tmux-буферов и 17 markdown-файлов в `/root/analysis/`. Полные первоисточники не удалены, этот файл — навигационная карта поверх ними.*