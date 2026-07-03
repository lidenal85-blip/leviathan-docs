# 🏛️ LEVIATHAN — СВОДНЫЙ МАСТЕР-ОТЧЁТ

**Дата:** 2026-07-03
**Сервер:** LEVIATHAN (78.17.24.96, Ubuntu 24.04, 4 ядра, 3.8 GB RAM, 30GB диск)
**Аудитор:** Buffy (Codebuff AI)
**Общая оценка архитектуры: 4.5/10** 🟡

---

## 📋 ИСТОЧНИКИ ДАННЫХ

1. **ARCHITECTURAL_AUDIT_2026-07-03.md** (545 строк) — аудит проектов /opt/
2. **SYSTEM_AUDIT_2026-07-03.md** (378 строк) — полный системный обзор
3. **CLAUDE_SESSIONS_ANALYSIS_2026-07-03.md** (170 строк) — анализ сессий Claude Code

---

## 1. 🖥️ АППАРАТНАЯ КОНФИГУРАЦИЯ

| Компонент | Значение | Статус |
|-----------|----------|--------|
| **CPU** | 4 ядра | 🟢 |
| **RAM** | 3.8 GB (2.5 GB used) | 🟡 66% |
| **Swap** | 5.5 GB (2.1 GB used) | 🔴 58% (цель <50%) |
| **Диск** | 30 GB ext4 (23 GB used) | 🟡 81% |
| **Uptime** | 4+ дней | 🟢 |
| **Load** | 2.16 / 1.92 / 1.75 | 🟢 OK |

**Swap файлы (3 шт):** /swapfile_new (3GB), /swapfile2g (2GB), /swapfile (512MB)

---

## 2. 📂 ПОЛНАЯ КАРТА ФАЙЛОВОЙ СИСТЕМЫ

```
/ (30GB ext4, 81%)
├── /usr/ (9.4 GB) — системные программы
│   ├── /usr/local/lib/python3.12/ (2.5 GB)
│   ├── /usr/local/x-ui/ (238 MB)
│   └── /usr/local/bin/ (143 MB)
├── /root/ (3.0 GB) — root home
│   ├── ai-interior-composer/ (391 MB, 26K файлов)
│   ├── free-claude-code/ (193 MB, 7.7K файлов)
│   ├── .claude/ (164 MB, 2206 файлов) — Claude Code
│   ├── .config/ (561 MB) + .cache/ (578 MB) + .local/ (553 MB)
│   ├── .u2net/ (168 MB) — ML модель
│   ├── .kimi-code/ (152 MB) — Kimi CLI
│   ├── Claude/ (46 MB) — репорты
│   └── analysis/ (808 KB) — аналитические отчёты
├── /opt/ (2.3 GB) — основные проекты (20+ проектов)
│   ├── leviathan_engine/ (144 MB) — ядро
│   ├── vocalplatform/ — вокал (3 сервиса, проблемы)
│   ├── arbitr_cockpit/ — арбитраж
│   └── 17+ других проектов
├── /var/ (2.0 GB) — данные
│   ├── www/ — 12 веб-проектов
│   ├── lib/postgresql/ (79 MB) — 6 БД
│   ├── log/journal/ (768 MB) — логи
│   └── log/nginx/ (1.3 MB)
├── /home/ — claude_worker (356 KB)
├── /tmp/ (211 MB) — chroma_db backup (107 MB)
└── /snap/ (358 MB) — core20, snapd
```

---

## 3. 🔧 СИСТЕМНЫЕ СЕРВИСЫ (17 активных)

### systemd
| Сервис | Статус |
|--------|--------|
| leviathan-daemon.service | ✅ |
| leviathan-hq.service | ✅ |
| leviathan_agent.service | ✅ |
| leviathan_mcp.service | ✅ |
| arbitr_cockpit.service | ✅ |
| den4ik-claude.service | ✅ |
| diet-platform.service | ✅ |
| fashion-stylist.service | ✅ |
| kwork-bot.service | ✅ |
| kwork-dispatcher.service | ✅ |
| pelleto.service | ✅ |
| savdabot.service | ✅ |
| vocal-bot.service | ✅ (но падает — apscheduler) |
| vocal-gateway.service | ⚠️ activating (FastAPIError) |
| vocal-web.service | ✅ |
| vocal-workers.service | ✅ |
| djvu2fb2_web.service | ✅ |

### Cron (основные)
- */5 min: survey-monitor.sh
- */10 min: f3_watchdog.sh
- */15 min: leviathan_alert.sh
- */30 min: task_queue.py
- */2h: fl_scraper.py
- */3h: proposal_bot.py
- Weekly: competitor_monitor.py, analytics_report.py
- Daily 04:00: savdabot reindex

### Открытые порты
22(SSH), 80/443(Nginx), 5434(PostgreSQL), 6379(Redis), 8000(Uvicorn), 8090(vocal-gateway), 8099(TorrServer), 8140(savdabot), 8400(secrets_vault), 8443(Xray), 2096/29585/4443(X-UI)

---

## 4. 📦 ВСЕ ПРОЕКТЫ (36+) — ПОЛНАЯ ИНВЕНТАРИЗАЦИЯ

### /opt/ проекты (20+)
| Проект | Размер | Статус | Приоритет | GitHub |
|--------|--------|--------|-----------|--------|
| leviathan_engine | 144 MB | 🟢 Active | 🔴 P0 | den4ikorm1985/leviathan-engine |
| secrets_vault | 795 LOC | 🟢 Active | 🔴 P0 | lidenal85-blip/secrets-vault |
| vocalplatform | ? | 🟡 Active (проблемы) | 🔴 P0 | — |
| arbitr_cockpit | 8K LOC | 🟡 Active | 🟡 P1 | den4ikorm1985/arbitr-cockpit |
| den4ik-claude | 5K LOC | 🟢 Active | 🟡 P1 | den4ikorm1985/den4ik-claude |
| savdabot | 6K LOC | 🟢 Active | 🟡 P1 | lewinthinkman-wq/SavdaBot |
| diet_platform | 5K LOC | 🟢 Active | 🟡 P1 | lidenal85-blip/diet_platform |
| triton_agent | 3.9K LOC | 🟢 Active | 🟡 P1 | — |
| vocal_bot | 32K LOC | 🔴 Не работает | 🟡 P1 | den4ikorm1985/vocal-bot |
| pelleto-ai | 5K LOC | 🟡 Maintenance | 🟢 P2 | den4ikorm1985/pelleto-ai |
| agent-hub | 141 LOC | 🔴 Dead | 🔴 P0 | — |
| dispatcher_simulator | 646K❓ | ❓ Unknown | 🔴 P0 | — |
| citrus_aura | 5K LOC | 🟡 Maintenance | 🟢 P2 | den4ikorm1985/citrus-aura |
| fri-messenger | 403 MB | 🟡 Active | 🟡 P1 | den4ikorm1985/fri-messenger |
| fashion-stylist | 277 MB | 🟡 Active | 🟡 P1 | — |
| kwork_bot | — | 🟢 Active | 🟡 P1 | den4ikorm1985/kwork-bot |
| kwork_dispatcher | — | 🟢 Active | 🟡 P1 | den4ikorm1985/kwork-dispatcher |
| media_factory | 3K LOC | 🟡 Maintenance | 🟢 P2 | den4ikorm1985/media-factory |
| hh_sniper | — | 🟡 Active | 🟢 P2 | — |
| leviathan-docs | 7K LOC | 🟢 Active | 🟡 P1 | lidenal85-blip/leviathan-docs |

### /root/ проекты (13)
| Проект | Размер | Файлов | Назначение |
|--------|--------|--------|------------|
| ai-interior-composer | 391 MB | 26,547 | Дизайн интерьера с ИИ (v1.0.0-P0) |
| free-claude-code | 193 MB | 7,761 | Claude Code CLI инструменты |
| Claude | 46 MB | 324 | Репорты и артефакты |
| backups | 17 MB | 136 | Бекапы |
| survey-finder | 4.1 MB | 622 | Мониторинг опросов |
| core | 2.6 MB | 32 | — |
| analysis | 784 KB | 31 | Аналитические отчёты |
| tszh-bot | 636 KB | 59 | ТСЖ Telegram бот |
| leviathan-property | 208 KB | 45 | — |
| freebuff | 152 KB | 11 | Freebuff CLI |

### /var/www/ проекты (12)
kinovibe, kinovibe_html, kinovibe_sub, voicestudio, voice_studio,
leviathan_hub, f3platform, outreach, hh_sniper, aic, narrative, html

### Базы данных
**PostgreSQL (6):** aic_composer, tg_monitor, vocalplatform, postgres, template0, template1
**SQLite:** ~10+ файлов по одному в каждом Python проекте
**ChromaDB:** savdabot (RAG), /tmp/ бэкап (107 MB)
**Redis:** установлен на порту 6379

---

## 5. 🧠 АНАЛИЗ СЕССИЙ CLAUDE CODE

### /root/.claude/ (164 MB, 2206 файлов)

| Компонент | Размер | Файлов |
|-----------|--------|--------|
| projects/ | 128 MB | 9 проектов |
| file-history/ | 26 MB | 1,512 |
| plugins/ | 5.8 MB | 376 |
| uploads/ | 2.2 MB | 10 |
| cache/ | 460 KB | 2 |
| paste-cache/ | 864 KB | 145 |
| backups/ | 244 KB | 5 |
| debug/ | 204 KB | 1 |
| plans/ | 20 KB | 2 |

### 9 проектов в .claude/projects/
| Проект | Размер | Сессий |
|--------|--------|--------|
| -root | 99 MB | 26 |
| -var-www-kinovibe | 23 MB | 5 |
| -var-www-kinovibe-src | 2.4 MB | — |
| -opt-pelleto-ai | 1.4 MB | 1 |
| -var-www-voice-studio | 1.5 MB | 1 |
| -opt-leviathan-engine-агент-service | 988 KB | — |
| -opt-hq | 36 KB | — |
| -opt-night-research | 32 KB | — |
| -root-free-claude-code | 100 KB | — |

### Топ-3 сессии
| ID | Размер | Суть |
|----|--------|------|
| 81d511b2... | **40 MB** | Docker + FastEmbed + Odysseus — настройка векторной памяти. Критическая ошибка oauth_org_not_allowed (403) в конце |
| 09f88a2e... | **24 MB** | SSH + Google Drive + bash-функции. Прерванные фоновые задачи |
| 68e2e0ed... | 7.5 MB | Анализ заказа "Арбитр кокпита", оценка стоимости |

### Всего: 66 session.jsonl, от 188 байт до 40 MB

---

## 6. 👥 ПОЛЬЗОВАТЕЛИ И ДОСТУП

| Пользователь | UID | Shell | Домашняя папка |
|-------------|-----|-------|---------------|
| root | 0 | bash | /root (3.0 GB) |
| claude_worker | 1000 | bash | /home/claude_worker (356 KB) |
| nova | 111 | sh | — |
| postgres | 113 | bash | /var/lib/postgresql |

**SSH ключи (root):** id_ed25519, levi_key, termux_rsa
**SSH: PermitRootLogin yes** — критическая уязвимость 🔴
**GitHub аккаунты:** den4ikorm1985 (15+ репо), lidenal85-blip, lewinthinkman-wq

---

## 7. 🔴 КРИТИЧЕСКИЕ ПРОБЛЕМЫ (P0)

| # | Проблема | Категория | Влияние |
|---|----------|-----------|---------|
| 1 | **Vault cutover не завершён** — секреты в os.environ | Безопасность | Компрометация всех AI-ключей |
| 2 | **Root SSH login разрешён** | Безопасность | Полный доступ к серверу |
| 3 | **Токены в git remote URL** | Безопасность | Утечка GitHub токенов |
| 4 | **Истекшая лицензия DEMO-SOTA-2026** | Лицензия | Ядро может отключиться |
| 5 | **vocal-gateway FastAPIError** | Стабильность | Сервис не стартует |
| 6 | **vocal_bot missing apscheduler** | Стабильность | Бот не работает |
| 7 | **Swap 58% (>50% target)** | Производительность | Деградация |
| 8 | **Диск 81%** | Производительность | Скоро закончится место |
| 9 | **agent-hub — заглушка с TODO** | Code debt | Мёртвый код |
| 10 | **dispatcher_simulator — 646K LOC, не git** | Unknown | Неизвестный проект |

---

## 8. 🟢 БЛИЖАЙШИЕ ДЕЙСТВИЯ (ROADMAP)

### День 1-2: Безопасность
1. Завершить Vault cutover
2. Отозвать и ротировать все ключи (SSH, GPG, GitHub, API)
3. Отключить PermitRootLogin
4. Очистить git remote URL от токенов

### День 3-4: Стабильность
5. Починить vocal-gateway (FastAPIError с UploadFile)
6. Починить vocal_bot (pip install apscheduler)
7. Обновить лицензию leviathan_engine

### День 5-7: Производительность
8. Объединить 3 swap файла в 1
9. Очистить /var/log/journal/ (768 MB)
10. Очистить /tmp/ (107 MB chroma backup)

### Неделя 2: Архитектура
11. Аудит dispatcher_simulator
12. Удалить agent-hub (или переписать)
13. Удалить мёртвые проекты
14. Написать README для всех проектов

### Неделя 3-4: Мониторинг
15. Prometheus Node Exporter
16. Health checks для всех сервисов
17. GitHub Actions CI/CD
18. Начать LAP или ADR-002

---

## 9. ❓ ВОПРОСЫ К ВЛАДЕЛЬЦУ

1. **LAP vs ADR-002?** — Ожидает решения (Денис/Максим)
2. **Что такое dispatcher_simulator?** — 646K LOC без git
3. **Нужен ли agent-hub?** — Заглушка с TODO
4. **Зачем citrus_aura?** — Назначение не определено
5. **Почему Docker не используется?** — Установлен, 0 контейнеров
6. **Планы по ai-interior-composer (391MB)?** — Крупнейший проект в /root/
7. **Кто такие @vaalchik?** — Упоминается в Pelleto MCP контексте
8. **Почему PostgreSQL на порту 5434?** — Нестандартный порт

---

## 10. 📊 СВОДНАЯ СТАТИСТИКА

| Метрика | Значение |
|---------|----------|
| Всего проектов | 36+ |
| Активных systemd сервисов | 17 |
| GitHub репозиториев | 20+ |
| Всего строк кода (прибл.) | 100K+ (без venv) |
| Всего файлов в системе | 30K+ |
| Баз данных | 6 PostgreSQL + 10+ SQLite + 1 ChromaDB |
| Пользователей с shell | 5 |
| Nginx сайтов | 15+ |
| Cron задач | 15+ |
| Сессий Claude Code | 66 (до 40MB каждая) |
| Проблем P0 | 10 |
| Оценка архитектуры | 4.5/10 🟡 |

---

## 📌 ФАЙЛЫ АУДИТА

Созданы в рамках этой сессии:
1. `/root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md`
2. `/root/analysis/SYSTEM_AUDIT_2026-07-03.md`
3. `/root/analysis/CLAUDE_SESSIONS_ANALYSIS_2026-07-03.md`
4. **`/root/analysis/LEVIATHAN_MASTER_REPORT_2026-07-03.md`** (этот файл)

---
*Документ создан: 2026-07-03*
*Аудитор: Buffy (Codebuff AI)*
