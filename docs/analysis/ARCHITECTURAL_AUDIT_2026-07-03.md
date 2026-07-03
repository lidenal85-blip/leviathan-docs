# 🏗️ ПОЛНЫЙ АРХИТЕКТУРНЫЙ АУДИТ LEVIATHAN PLATFORM

**Дата:** 2026-07-03
**Сервер:** LEVIATHAN (78.17.24.96, Ubuntu 24.04, 4 ядра, 3.8 GB RAM)
**Аудитор:** Buffy (Codebuff AI Agent)
**Продолжительность:** ~2 часа (автоматизированный анализ)

---

## 1. EXECUTIVE SUMMARY

### Краткое состояние платформы

Платформа LEVIATHAN представляет собой **разросшуюся экосистему из 15+ проектов**, построенную вокруг центрального ядра `leviathan_engine`. Система эволюционировала органически, без единого архитектурного плана, что привело к значительной фрагментации, дублированию и техническому долгу.

**Оценка архитектуры: 4.5/10** 🟡
- Соответствие Манифесту v5.0.0: ~9.5/20 баллов
- 15+ активных systemd сервисов
- 6+ разных баз SQLite
- Критические проблемы безопасности (секреты в коде)

### Top-5 критических проблем 🔴

| # | Проблема | Влияние |
|---|----------|---------|
| 1 | **Секреты в открытом доступе** — API ключи в .env и коде | Компрометация всех AI-сервисов |
| 2 | **Swap 58%** (2.1/5.0 GB) — не достигает цели <50% | Деградация производительности |
| 3 | **Фрагментация архитектуры** — 15+ микросервисов без единого стандарта | Рост сложности, bus factor |
| 4 | **Отсутствие документации** — README только у 5 из 15 проектов | Bus factor критический |
| 5 | **Vault cutover не завершён** — секреты всё ещё читаются из os.environ | Безопасность не закрыта |

### Top-5 возможностей 🟢

| # | Возможность | Потенциал |
|---|-------------|-----------|
| 1 | **Консолидация проектов** — объединить overlapping сервисы | -50% complexity |
| 2 | **Завершение Vault cutover** — централизовать secrets | Безопасность + аудит |
| 3 | **Переход на v5.0.0** — FSM, Runnable Interface, Middleware | Индустриальный standard |
| 4 | **Мониторинг и observability** — добавить метрики и алерты | Проактивное управление |
| 5 | **Cleanup мёртвых проектов** — удалить заброшенное | -30% maintenance overhead |

### Рекомендуемый roadmap на 30 дней

**Неделя 1 (P0):** Завершить Vault cutover → Очистка swap → Fix vocal-gateway
**Неделя 2 (P1):** Аудит и удаление мёртвых проектов → Стандартизация конфигов
**Неделя 3 (P1):** Консолидация agent-hub + den4ik-claude → Документация
**Неделя 4 (P2):** Мониторинг (Prometheus + Grafana) → Тесты → CI/CD

---

## 2. ИНВЕНТАРИЗАЦИЯ ПРОЕКТОВ

### 2.1 leviathan_engine (ЯДРО)

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/leviathan_engine/` |
| **Размер** | ~144 MB |
| **Язык/Стек** | Python, FastAPI, SQLite |
| **Статус** | 🟢 Active |
| **Назначение** | Ядро платформы — AI Code Doctor, daemon, MCP сервер |
| **Последний коммит** | 2026 (ветка main) |
| **GitHub** | https://github.com/LeviAl85/ |
| **Сервисы** | `leviathan-daemon.service`, `leviathan_agent.service`, `leviathan_mcp.service`, `leviathan-hq.service` |
| **Запущен как** | systemd (4 сервиса) |
| **Конфиги** | `leviathan_config.json`, `licenses.json` |
| **База данных** | SQLite (контекст, сессии, идемпотентность) |
| **Документация** | ❌ Нет README.md |
| **Тесты** | ❌ Не обнаружены |

**Ключевые компоненты:**
- `agent/` — ядро агента (121 файл, ~25K LOC)
- `mcp_server/` — MCP сервер
- `heads/` — специализированные "головы"
- `execution/` — пайплайны выполнения
- `db/` — системы хранения

**Лицензия:** DEMO-SOTA-2026 (истекла 01.07.2026) 🔴

---

### 2.2 triton_agent (Sport Observer)

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/triton_agent/` |
| **Размер** | 3.9K LOC |
| **Язык/Стек** | Python, Groq, Gemini |
| **Статус** | 🟢 Active |
| **Назначение** | AI-агент для футбольных трансляций в Telegram |
| **Зависимости** | API-Football, FIFA.com, Flashscore |
| **Последний коммит** | 2026 |
| **Сервис** | В составе leviathan_agent? |
| **Документация** | ✅ Есть README.md |
| **Тесты** | ❌ Не обнаружены |

**Архитектура:**
- `MultiSourceProvider` — сбор данных из 4+ источников
- `FootballRules` — нормализация событий
- `ContentOrchestrator` — обработка с LLM
- `ContentValidator` — защита от галлюцинаций

---

### 2.3 arbitr_cockpit

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/arbitr_cockpit/` |
| **Размер** | ~8K LOC (без venv) |
| **Язык/Стек** | Python, FastAPI, SQLAlchemy 2.0, SQLite |
| **Статус** | 🟡 Active — v0.1 |
| **Назначение** | Управление конвейером AI-ролей Kwork Arbitr |
| **Последний коммит** | 2026 |
| **Сервис** | `arbitr_cockpit.service` |
| **Документация** | ✅ Есть README.md |
| **Тесты** | ❌ Не обнаружены |
| **Режимы** | manual (copy-paste) + auto (нужен keypool) |

---

### 2.4 den4ik-claude (LEVIATHAN HUB)

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/den4ik-claude/` |
| **Размер** | 5K LOC |
| **Язык/Стек** | Python, Telegram Bot API |
| **Статус** | 🟢 Active |
| **Назначение** | Левиафан ХАБ — Telegram бот (AUTO/CORE/FORGE/HERALD) |
| **Сервис** | `den4ik-claude.service` |
| **Документация** | ❌ Нет README.md |
| **Тесты** | ❌ Не обнаружены |

---

### 2.5 diet_platform (Пухляш)

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/diet_platform/` |
| **Размер** | 5K LOC |
| **Язык/Стек** | Python, FastAPI |
| **Статус** | 🟢 Active |
| **Назначение** | Diet Platform — FastAPI + Telegram Bot |
| **Сервис** | `diet-platform.service` |
| **Документация** | ❌ Нет README.md |
| **Тесты** | ❌ Не обнаружены |

---

### 2.6 pelleto-ai

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/pelleto-ai/` |
| **Размер** | 5K LOC |
| **Язык/Стек** | Python, FastAPI, Gemini, aiogram 3 |
| **Статус** | 🟡 Maintenance |
| **Назначение** | Лендинг продажи пеллет с AI-агентом |
| **Сервис** | `pelleto.service` |
| **Документация** | ✅ Есть README.md |
| **Тесты** | ❌ |

---

### 2.7 savdabot

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/savdabot/` |
| **Размер** | 6K LOC |
| **Язык/Стек** | Python, FastAPI, ChromaDB (RAG), Groq |
| **Статус** | 🟢 Active |
| **Назначение** | AI-консультант для savda.ru |
| **Сервис** | `savdabot.service` |
| **Порт** | 8140 |
| **Документация** | ✅ Есть README.md |
| **Тесты** | ❌ |

---

### 2.8 vocalplatform

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/vocalplatform/` |
| **Язык/Стек** | Python, FastAPI |
| **Статус** | 🟡 Active — проблемы |
| **Сервисы** | `vocal-gateway.service` (activating), `vocal-web.service`, `vocal-workers.service` |
| **Порт** | 8090 (gateway) |
| **Документация** | ❌ |
| **Известные проблемы** | FastAPIError: Invalid args for response field (UploadFile) |

---

### 2.9 vocal_bot

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/vocal_bot/` |
| **Размер** | 32K LOC |
| **Язык/Стек** | Python |
| **Статус** | 🔴 Не работает |
| **Назначение** | Telegram AI наставник по вокалу |
| **Сервис** | `vocal-bot.service` |
| **Известные проблемы** | `ModuleNotFoundError: No module named 'apscheduler'` |

---

### 2.10 agent-hub

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/agent-hub/` |
| **Размер** | 141 LOC |
| **Язык/Стек** | Python, FastAPI, SQLite |
| **Статус** | 🔴 Dead (заглушка) |
| **Назначение** | Мультиклиентный хаб для AI-агентов |
| **TODO** | `# TODO: вставь сюда логику консультанта` |
| **Документация** | ❌ |

---

### 2.11 secrets_vault

| Параметр | Значение |
|----------|----------|
| **Путь** | `/opt/secrets_vault/` |
| **Размер** | 795 LOC |
| **Язык/Стек** | Python, FastAPI, AES-256 (Fernet) |
| **Статус** | 🟢 Active |
| **Назначение** | Централизованное хранилище секретов |
| **Порт** | 8400 |
| **Сервис** | Не найден в systemd |
| **Документация** | ❌ |
| **Статус интеграции** | Vault cutover НЕ ЗАВЕРШЁН (P0) |

---

### 2.12 Прочие проекты

| Проект | Путь | Статус | Назначение |
|--------|------|--------|------------|
| **fashion-stylist** | `/opt/fashion-stylist/` | 🟡 Active | AI Fashion Stylist API |
| **fri-messenger** | `/opt/fri-messenger/` | 🟡 Active | Messenger |
| **citrus_aura** | `/opt/citrus_aura/` | 🟡 Maintenance | — |
| **dispatcher_simulator** | `/opt/dispatcher_simulator/` | ❓ Unknown | — |
| **kwork_bot** | `/opt/kwork_bot/` | 🟢 Active | Kwork Dispatcher Bot |
| **kwork_dispatcher** | `/opt/kwork_dispatcher/` | 🟢 Active | Kwork Dispatcher API |
| **media_factory** | `/opt/media_factory/` | 🟡 Maintenance | — |
| **hh_sniper** | `/opt/hh_sniper/` | 🟡 Active | HeadHunter sniper |
| **den4ik_hq** | `/opt/den4ik_hq/` | ❓ Unknown | Flutter приложение |
| **freebuff** | system | 🟢 Active | CLI логирование |

---

## 3. МАТРИЦА ПРИОРИТЕТОВ

| Проект | Приоритет | Обоснование | Действие |
|--------|-----------|-------------|----------|
| **leviathan_engine** | 🔴 P0 | Ядро платформы, 4 сервиса | Сохранить и развивать |
| **secrets_vault** | 🔴 P0 | Безопасность, незавершённый cutover | Завершить интеграцию |
| **vocalplatform** | 🔴 P0 | Сервис не стартует (gateway) | Починить gateway |
| **den4ik-claude** | 🟡 P1 | Основной Telegram HUB | Поддерживать |
| **arbitr_cockpit** | 🟡 P1 | Активно используется | Поддерживать |
| **triton_agent** | 🟡 P1 | Работает стабильно | Поддерживать |
| **savdabot** | 🟡 P1 | Работает, внешний клиент | Поддерживать |
| **diet_platform** | 🟡 P1 | Работает | Поддерживать |
| **fashion-stylist** | 🟡 P1 | Работает | Поддерживать |
| **kwork_bot/dispatcher** | 🟡 P1 | Работает | Поддерживать |
| **pelleto-ai** | 🟢 P2 | Экспериментальный | Заморозить |
| **citrus_aura** | 🟢 P2 | Неизвестно назначение | Аудит → заморозка |
| **media_factory** | 🟢 P2 | Мало активности | Аудит → удаление |
| **agent-hub** | 🔴 P0 | Заглушка, не работает | Удалить или переписать |
| **vocal_bot** | 🟡 P1 | Не работает (apscheduler) | Починить или удалить |
| **hh_sniper** | 🟢 P2 | Специализированный | Поддерживать |
| **den4ik_hq** | 🟢 P2 | Flutter, не используется | Аудит |
| **dispatcher_simulator** | 🔴 P0 | 646K LOC, не git, не ясно | Срочный аудит |

---

## 4. АНАЛИЗ АКТУАЛЬНОСТИ ДОКУМЕНТАЦИИ

### /root/analysis/ — полный список

| Документ | Актуальность | Соответствие коду | Рекомендация |
|----------|-------------|-------------------|-------------|
| `MY_SYSTEM_ANALYSIS.md` | 🟡 Частично | Частично | Обновить |
| `SECRETS_ANALYSIS.md` | 🟢 Актуален | Да | Дополнить Vault |
| `ALL_ANALYSIS.md` | 🟡 Частично | Частично | Обновить после v5.0.0 |
| `LOGS_ANALYSIS.md` | 🟢 Актуален | Да | Оставить |
| `IMPLEMENTATION_REPORT.md` | 🟢 Актуален | Да | Оставить |
| `ADR_002_MERGE_PLAN.md` | 🟢 Актуален | Да | Ожидает решения |
| `ADR-002/ADR-002-FINAL.md` | 🟢 Актуален | Да | Ожидает решения |
| `COMPREHENSIVE_UNIFICATION_PLAN.md` | 🟡 Частично | Частично | Обновить |
| `AGENT_MERGE_PLAN.md` | 🟡 Частично | Частично | Обновить |
| `NEXT_SESSION_PROMPT.md` | 🟢 Актуален | Да | Оставить |
| `SESSION_CONTEXT_2026-07-02.md` | 🟡 Частично | Да | Обновить |
| `EXECUTION_PLAN.md` | 🟡 Частично | Частично | Обновить |
| `MANIFEST_MIGRATION_PLAN.md` | 🟡 Частично | Частично | Обновить |
| `TASK_PROMPT.md` | 🟢 Актуален | Да | Оставить |
| `git.md` | 🟡 Частично | Частично | Обновить |
| `logs.md` | 🟢 Актуален | Да | Оставить |
| `restore.md` | 🟢 Актуален | Да | Оставить |

### /opt/leviathan-docs/

MkDocs проект с документацией. Есть `mkdocs.yml`, `docs/`, `site/`. Требуется ревью содержимого.

---

## 5. ТЕХНИЧЕСКИЙ ДОЛГ

### Top-10 критических проблем

| # | Проблема | Проект | Оценка времени | Влияние |
|---|----------|--------|---------------|---------|
| 1 | **Истекшая лицензия** (DEMO-SOTA-2026, expired 01.07) | leviathan_engine | 1 час | Критическое |
| 2 | **Vault cutover не завершён** | Все | 4 часа | Безопасность |
| 3 | **vocal-gateway FastAPIError** | vocalplatform | 1 час | Сервис не стартует |
| 4 | **vocal_bot missing apscheduler** | vocal_bot | 30 мин | Сервис не работает |
| 5 | **Swap 58% > target 50%** | Система | 30 мин | Производительность |
| 6 | **Нет тестов ни в одном проекте** | Все | 1 неделя+ | Качество кода |
| 7 | **Нет README в 10 из 15 проектов** | Все | 4 часа | Bus factor |
| 8 | **agent-hub — мёртвый код** | agent-hub | 30 мин | Заглушка |
| 9 | **Секреты в git-истории** | Все | Сложно | Необратимо |
| 10 | **15+ сервисов без мониторинга** | Все | 2 дня | Нет observability |

---

## 6. БЕЗОПАСНОСТЬ

### Найденные проблемы

| # | Проблема | Серьёзность | Статус |
|---|----------|------------|--------|
| 1 | GitHub токен (`ghp_...`) в .env | 🔴 Critical | Частично решено (Vault) |
| 2 | 6 Gemini API ключей в открытом доступе | 🔴 Critical | Частично решено |
| 3 | 5 Groq API ключей в открытом доступе | 🔴 Critical | Частично решено |
| 4 | Telegram Bot токены в коде | 🔴 Critical | Частично решено |
| 5 | Дублирование ключей между проектами | 🟡 High | Не решено |
| 6 | Отсутствие ротации ключей | 🟡 High | Не решено |
| 7 | Нет шифрования .env файлов | 🟡 High | Vault решает |
| 8 | Нет аудита доступа к ключам | 🟡 High | Vault решает |

### Критические рекомендации

1. **НЕМЕДЛЕННО** завершить Vault cutover (P0)
2. Отозвать и ротировать все скомпрометированные ключи
3. Очистить git-историю от секретов (или сменить все ключи)
4. Добавить `.env` в `.gitignore` всех проектов

---

## 7. ПРОИЗВОДИТЕЛЬНОСТЬ

### Текущие метрики

| Метрика | Значение | Целевое | Статус |
|---------|----------|---------|--------|
| RAM used | 66% (2.5/3.8 GB) | <60% | 🟡 Close |
| RAM available | 1.3 GB | >1.5 GB | 🟡 Close |
| Swap used | 58% (2.1/5.0 GB) | <50% | 🔴 Не достигнуто |
| CPU Load (1m) | 2.16 | <4.0 | 🟢 OK |
| CPU Load (5m) | 1.92 | <4.0 | 🟢 OK |
| CPU Load (15m) | 1.75 | <4.0 | 🟢 OK |
| Disk used | 81% (23/30 GB) | <80% | 🟡 Почти критично |
| Uptime | 4d 8h | — | 🟢 OK |

### Узкие места

1. **Swap 58%** — несмотря на частичную очистку, остаётся выше цели
2. **Диск 81%** — критично. Нужно очистить логи и временные файлы
3. **15+ сервисов на Python** — каждый потребляет RAM
4. **Порт 8765 конфликт** — систематическая ошибка bind в journalctl

---

## 8. ДУБЛИРОВАНИЕ И ПЕРЕСЕЧЕНИЯ

### Overlapping проекты

| Группа | Проекты | Пересечение | Рекомендация |
|--------|---------|-------------|-------------|
| **Telegram боты** | den4ik-claude, diet_platform, vocal_bot, savdabot, kwork_bot | Каждый — отдельный бот | Оправдано разными функциями |
| **FastAPI сервисы** | arbitr_cockpit, agent-hub, savdabot, pelleto-ai, secrets_vault | Все на FastAPI, разные порты | Унифицировать шаблон |
| **AI агенты** | leviathan_engine, triton_agent, agent-hub | Разные уровни абстракции | agent-hub — удалить |
| **Хранилища ключей** | .env файлы, secrets_vault | Дублирование | Vault cutover решит |

### Дублирование кода

- **Gemini/Groq ключи** — одинаковые ключи в .env 4+ проектов
- **Логика работы с LLM** — повторяется в каждом проекте
- **SQLite БД** — отдельная БД в каждом проекте

---

## 9. ВОПРОСЫ К ВЛАДЕЛЬЦУ

1. **Каково назначение `citrus_aura` и `dispatcher_simulator`?**
2. **Кто является владельцем каждого проекта?** — Все сервисы запущены от root
3. **Почему не используется Docker?** — Docker установлен, но 0 контейнеров
4. **Каковы планы по `fri-messenger` (403 MB) и `fashion-stylist` (277 MB)?**
5. **Почему PostgreSQL на порту 5434 (не стандартный 5432)?**
6. **Нужен ли `agent-hub`?** — Код — заглушка с TODO
7. **Планируется ли CI/CD?** — GitHub Actions не настроены
8. **Каков статус `dispatcher_simulator` (не git)?**
9. **Актуальна ли лицензия?** — DEMO-SOTA-2026 истекла 01.07.2026
10. **Будет ли выбран LAP или ADR-002?** — Ожидает решения (P2)

---

## 10. ROADMAP РЕКОМЕНДАЦИИ

### Неделя 1 (P0 — Экстренные действия)

| День | Задача | Проект |
|------|--------|--------|
| 1 | 🔴 Завершить Vault cutover | Все |
| 2 | 🔴 Отозвать и ротировать ключи | Все |
| 3 | 🔴 Починить vocal-gateway | vocalplatform |
| 4 | 🔴 Починить vocal_bot (apscheduler) | vocal_bot |
| 5 | 🔴 Очистить swap + освободить диск | Система |
| 6 | 🟡 Обновить лицензию | leviathan_engine |
| 7 | 🟡 Добавить .env в .gitignore | Все |

### Месяц 1 (P1 — Консолидация)

- Удалить мёртвые проекты: `agent-hub`, `citrus_aura`
- Написать README для всех проектов без документации
- Стандартизировать шаблон FastAPI сервиса
- Настроить единое логирование (JSON)
- Добавить health checks для всех сервисов
- Установить Prometheus Node Exporter

### Квартал 1 (P2 — Оптимизация)

- Выбрать LAP или ADR-002 и начать реализацию
- Написать тесты для leviathan_engine
- Настроить GitHub Actions (lint + test)
- Консолидировать дублирующиеся LLM-вызовы
- Добавить rate limiting
- Решить проблему диска

### Год 1 (Стратегическое развитие)

- Переход на leviathan_engine v5.0.0 (FSM, Runnable, Middleware)
- Внедрение Docker Compose для изоляции сервисов
- Добавление staging окружения
- Масштабирование: увеличение RAM до 8+ GB
- OpenAPI/Swagger для всех REST API

---

## 11. ПРИЛОЖЕНИЯ

### A. Матрица зависимостей проектов

```
leviathan_engine ←── triton_agent
                 ←── den4ik-claude
                 ←── arbitr_cockpit (keypool)
                 ←── agent-hub (.env shared)
                 
secrets_vault  ←── leviathan_engine (частично)
               ←── triton_agent (частично)
               ←── pelleto-ai (частично)
                
vocalplatform  ──→ vocal_bot (связаны?)
               ──→ vocal_web

kwork_bot      ──→ kwork_dispatcher
```

### B. Системные сервисы (15+ активных)

| Сервис | Статус |
|--------|--------|
| `arbitr_cockpit.service` | ✅ active |
| `den4ik-claude.service` | ✅ active |
| `diet-platform.service` | ✅ active |
| `djvu2fb2_web.service` | ✅ active |
| `fashion-stylist.service` | ✅ active |
| `kwork-bot.service` | ✅ active |
| `kwork-dispatcher.service` | ✅ active |
| `leviathan-daemon.service` | ✅ active |
| `leviathan-hq.service` | ✅ active |
| `leviathan_agent.service` | ✅ active |
| `leviathan_mcp.service` | ✅ active |
| `pelleto.service` | ✅ active |
| `savdabot.service` | ✅ active |
| `vocal-bot.service` | ✅ active (но падает) |
| `vocal-gateway.service` | ⚠️ activating |
| `vocal-web.service` | ✅ active |
| `vocal-workers.service` | ✅ active |

### C. Открытые порты (ключевые)

| Порт | Сервис |
|------|--------|
| 22 | SSH |
| 80, 443 | Nginx |
| 5434 | PostgreSQL |
| 6379 | Redis |
| 8000 | Uvicorn |
| 8090 | vocal-gateway |
| 8099 | TorrServer |
| 8140 | savdabot |
| 8400 | secrets_vault |
| 8443 | Xray |
| 2096, 29585, 4443 | X-UI |

### D. Cron задачи (ключевые)

| Расписание | Команда | Назначение |
|-----------|---------|------------|
| */5 min | survey-monitor.sh | Мониторинг |
| */10 min | f3_watchdog.sh | Watchdog |
| */15 min | leviathan_alert.sh | Алерты |
| */30 min | task_queue.py | Очередь задач |
| */2 hour | fl_scraper.py | Сбор данных |
| */3 hour | proposal_bot.py | Предложения |
| Weekly | competitor_monitor.py | Мониторинг конкурентов |
| Weekly | analytics_report.py | Аналитика |
| Daily 04:00 | savdabot reindex | Переиндексация |

---

## ИТОГО

**Оценка архитектуры: 4.5/10** 🟡

**Критические действия (P0):**
1. 🟡 Vault cutover (начат, не завершён)
2. 🔴 Ротация ключей
3. 🔴 Починка vocal-gateway и vocal_bot
4. 🔴 Очистка swap и диска
5. 🔴 Обновление лицензии

---
*Документ создан: 2026-07-03*
*Аудитор: Buffy (Codebuff AI)*
*Файл: /root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md*
ENDOFFILE && echo "SUCCESS: File created" && wc -l /root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md

---

## 12. ПОЛНЫЙ РЕЕСТР ПРОЕКТОВ (ПО МЕТОДОЛОГИИ PROMPT2.MD)

Аудит проведён по методологии prompt2.md — поиск проектов ВО ВСЕХ директориях, не только в /opt/.

**Найдено проектов: 58**

| Директория | Проектов |
|-----------|---------|
| /opt/ | 25 |
| /root/ | 13 |
| /var/www/ | 12 |
| systemd сервисы | 22 |
| Nginx сайты | 12 |
| Cron задачи | 15+ |

### Структура файлов
| Файл | Описание |
|------|----------|
| `/root/analysis/audit/PROJECT_LIST.md` | Полный реестр 58 проектов с Tier-классификацией |
| `/root/analysis/audit/*_audit.md` | 37 индивидуальных аудитов проектов |
| `/root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md` | Главный архитектурный аудит |
| `/root/analysis/SYSTEM_AUDIT_2026-07-03.md` | Полный системный обзор |
| `/root/analysis/CLAUDE_SESSIONS_ANALYSIS_2026-07-03.md` | Анализ сессий Claude Code |
| `/root/analysis/LEVIATHAN_MASTER_REPORT_2026-07-03.md` | Сводный мастер-отчёт |

### Распределение по Tier
- **Tier 1 (Критичные):** 18 проектов — systemd, активный git, продакшн
- **Tier 2 (Важные):** 15 проектов — git, недавняя активность
- **Tier 3 (Периферийные):** 17 проектов — старые, маленькие
- **Tier 4 (Мёртвые):** 8 проектов — без коммитов, без README
