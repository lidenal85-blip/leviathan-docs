# 🏗️ ПОЛНЫЙ АУДИТ СЕРВЕРА И ПЛАН ОБЪЕДИНЕНИЯ В ЦЕЛОСТНЫЙ ОРГАНИЗМ

**Сервер:** LEVIATHAN (78.17.24.96)  
**Дата:** 2026-07-02  
**Статус:** COMPREHENSIVE AUDIT v1.0  
**Охват:** 30+ проектов, ~50 системных сервисов, 20+ БД, 30+ портов

---

## 📋 1. EXECUTIVE SUMMARY

### 1.1 Состояние системы

| Метрика | Значение | Статус |
|---------|----------|--------|
| Проектов в /opt | 52 | 🟢 |
| Python-файлов (суммарно) | ~8,000+ | 🟡 |
| Строк кода | ~50,000+ (без free-claude-code) | 🟡 |
| Системных сервисов | ~50+ (20+ кастомных) | 🟢 |
| Открытых портов | 30+ | 🟡 |
| Баз данных SQLite | 20+ | 🟠 |
| Процессов freebuff | 5 (по 7-12% RAM каждый) | 🔴 |
| Диск | 23/30G (80%) | 🟡 |
| RAM | 2.5/3.8 GiB (66%) | 🟡 |
| Swap | 4.4/5.0 GiB (88%) | 🔴 |
| Load avg | ~10+ на 4 ядра | 🔴 |

### 1.2 Оценка архитектуры: 4/10 ⚠️

**Сильные стороны:**
- ✅ Богатая экосистема: 30+ проектов, интеграции с Telegram, MCP, HTTP
- ✅ Secrets Vault на порту 8400 (AES-256)
- ✅ ModelRouter с AUTO-режимом и fallback
- ✅ EventBus pub/sub
- ✅ Manifest 5.1.0 — формальный lifecycle, memory, workflow

**Критические проблемы:**
- 🔴 **Нет централизованного логирования** — каждый проект пишет в своё
- 🔴 **Ключи разбросаны** — TG_BOT_TOKEN, GITHUB_TOKEN, .env файлы в 7+ местах
- 🔴 **freebuffd жрёт ресурсы** — 5 процессов по 10% RAM каждый, циклические рестарты
- 🔴 **Swap под 88%** — систем под нагрузкой, load 10+
- 🔴 **Нет единого LLM Gateway** — 5+ независимых реализаций
- 🔴 **Нет единой системы авторизации** — каждый сервис сам по себе
- 🔴 **20+ SQLite БД** — нет централизации, каждая со своей схемой
- 🔴 **125KB bot.py монолит** (den4ik-claude) — невозможно поддерживать

---

## 2. 🔍 ПОЛНЫЙ АУДИТ СИСТЕМЫ

### 2.1 Все сервисы

#### 🟢 Активные (Production)

| Сервис | Назначение | Порт | Зависимости |
|--------|-----------|------|-------------|
| **leviathan-daemon** | Leviathan Engine Daemon | — | Python 3.12 |
| **leviathan_agent** | Leviathan Agent | :8200 | providers, tools, MCP |
| **leviathan_mcp** | MCP Server | :8300 | leviathan_agent |
| **leviathan-hq** | Leviathan HQ Web | :8120 | leviathan_agent |
| **den4ik-claude** | Telegram Bot (Leviathan Hub) | TG only | Pyrogram |
| **triton_agent** | Sport Observer | :8201 | LLM Gateway |
| **vocal-bot** | Vocal AI Bot | — | VocalPlatform |
| **vocal-gateway** | Vocal Gateway | — | vocal-bot |
| **vocal-web** | Vocal Web UI | — | vocal-gateway |
| **vocal-workers** | Vocal Workers | — | vocal-web |
| **fashion-stylist** | AI Fashion Stylist | — | LLM |
| **savdabot** | SavdaBot | — | Postgres, Redis |
| **arbitr_cockpit** | Arbitr Dashboard | :8090 | FastAPI, SQLAlchemy |
| **diet_platform** | Diet Platform «Пухляш» | — | FastAPI + TG |
| **pelleto** | Pelleto AI | :8131 | FastAPI, MCP |
| **fcc-server** | Free Claude Code | :8083 | Claude API |
| **sport-observer** | AI Football Agent | :8201 | Triton |
| **voicestudio** | VoiceStudio | — | FastAPI |
| **kinovibe** | KinoVibe Backend | — | FastAPI |
| **secrets-vault** | Secrets Vault API | :8400 | AES-256 |
| **survey-finder** | Survey Finder | — | FastAPI |
| **hh_sniper** | HH Sniper | — | FastAPI |
| **minusfabrika** | Gemini Proxy | — | Gemini API |
| **djvu2fb2_web** | DjVu → FB2 Converter | — | Web UI |
| **torrserver** | TorrServer | :8099 | — |
| **openclaw-gateway** | OpenClaw Gateway | — | Node.js |
| **x-ui** | X-UI Panel | :2096 | x-ray |
| **nginx** | Reverse Proxy | :80, :443 | 12 сайтов |
| **postgresql** | PostgreSQL | :5434 | — |
| **redis-server** | Redis | :6379 | — |

#### 🔴 Проблемные

| Сервис | Проблема | Влияние |
|--------|----------|---------|
| **freebuffd** | Циклический рестарт (exit code 1), 5 процессов | 40% RAM, аномальный load |
| **citrus_aura** | DOWN | Недоступен |
| **lawu** | DOWN | Недоступен |
| **arbitr** | DOWN (но arbitr_cockpit работает) | ? |
| **agent-hub** | INACTIVE | — |
| **pelleto MCP** | fastmcp не установлен | MCP-инструменты недоступны |

### 2.2 Все проекты (по размеру кодовой базы)

| Проект | Python файлов | LOC | Приоритет |
|--------|-------------|-----|-----------|
| **Leviathan Engine** | 1,941 | ~23,627 | 🔴 P0 — ЯДРО |
| **Dispatcher Simulator** | 1,359 | ~? | 🟡 P3 |
| **Triton Agent** | 45 | ~3,905 | 🔴 P0 — CORE AGENT |
| **Vocal Bot** | 49 | ~? | 🟡 P2 |
| **HH Sniper** | 48 | ~? | 🟢 P4 |
| **Diet Platform** | 43 | ~? | 🟡 P2 |
| **Pelleto AI** | 37 | ~4,730 | 🟡 P2 |
| **Vocal Platform** | 34 | ~? | 🟡 P2 |
| **Arbitr Cockpit** | 27 | ~3,169 | 🟡 P2 |
| **SavdaBot** | 25 | ~? | 🟡 P2 |
| **Media Factory** | 24 | ~? | 🟡 P2 |
| **Citrus Aura** | 20 | ~? | 🟢 P4 |
| **Secrets Vault** | 19 | ~? | 🔴 P0 — SECURITY |
| **Fashion Stylist** | 15 | ~? | 🟢 P4 |
| **den4ik-claude** | 8 | ~5,098 | 🔴 P0 — TG INTERFACE |
| **Freebuff** | 6 | ~891 | 🟡 P2 — AUDIT |

### 2.3 Базы данных (20+ SQLite, 1 Postgres)

```
Leviathan Engine (8 БД):
  ├── leviathan.db (18MB) — основная
  ├── context_memory.db (208K) — память контекста
  ├── leviathan_kb.db (392K) — база знаний
  ├── leviathan_routes.db (36K) — маршруты
  ├── claude_sessions.db (24K) — сессии Claude
  ├── claude_accounts.db (32K) — аккаунты Claude
  ├── claude_projects.db (20K) — проекты Claude
  └── mcp_gateway.db — MCP шлюз

Остальные проекты:
  ├── /opt/arbitr_cockpit/data/arbitr.db
  ├── /opt/savdabot/data/ (6 БД: competitors, cards, query_cache, feedback, analytics, catalog)
  ├── /opt/diet_platform/diet_platform.db
  ├── /opt/pelleto-ai/data/pelleto.db
  ├── /opt/media_factory/db/media_factory.db
  ├── /opt/citrus_aura/pearl.db
  ├── /opt/hh_sniper/data/hh_sniper.db
  ├── /opt/filebrowser/fb.db
  ├── /opt/agent-hub/data/hub.db
  └── /opt/secrets_vault/data/vault.db
```

### 2.4 Инфраструктура LLM

```
LLM Providers (5+ независимых реализаций):
├── Leviathan Engine
│   ├── GeminiProvider (HTTP, httpx) — основной
│   ├── GroqProvider (groq SDK) — fallback
│   ├── ClaudeProvider (ClaudeAdapter) — fallback
│   └── ModelRouter — AUTO/GEMINI/CLAUDE/GROQ/FULL режимы
├── Triton Agent
│   └── ProductionLLMGateway — Groq→Gemini→Leviathan chain
├── den4ik-claude
│   └── Встроенный LLM-вызов (свой формат)
├── Pelleto
│   └── Встроенный (через FastAPI)
└── Gemini Proxy (:8083)
    └── Anthropic API → Gemini API конвертер
```

### 2.5 Система логирования

```
Логи (разрозненные):
├── Systemd journal — все сервисы пишут в journal
├── /var/log/
│   ├── den4ik_claude.log
│   ├── djvu2fb2_web.log
│   └── f3_watchdog.log
├── /opt/leviathan_engine/.leviathan/logs/
│   ├── daemon.log
│   └── daemon_error.log
├── /opt/leviathan_engine/agent_service/logs/
│   ├── pipeline.log
│   └── claude_manager.log
├── /root/freebuff/state/
│   ├── chains/ (AES-256-шифрованные логи команд)
│   └── checkpoints/ (подписанные)
└── EventBus (6 событий)
    ├── task_queued
    ├── task_started
    ├── task_done
    ├── task_failed
    ├── task_evaluated
    └── worker_spawned
```

### 2.6 Секреты и ключи

```
Ключи разбросаны по .env файлам (7+ мест):
├── /opt/leviathan_engine/agent_service/.env
│   ├── TG_BOT_TOKEN=86046...
│   ├── LEV_MCP_TOKEN=den4ik1985!
│   └── GITHUB_TOKEN=ghp_G9J...
├── /root/.env
├── /root/free-claude-code/.env
├── /root/.fcc/.env
├── /root/leviathan-property/.env
├── /root/ai-interior-composer/.env
├── /root/tszh-bot/.env
├── /root/survey-finder/.env
├── SSH keys (~/.ssh/): id_ed25519, levi_key, termux_rsa
└── Secrets Vault (:8400) — работает, но не все используют
```

---

## 3. 🎯 ПРИОРИТЕТЫ ПО ВАЖНОСТИ

### P0: Критическая инфраструктура (делать сейчас)

| # | Что | Почему | Системы |
|---|-----|--------|---------|
| 1 | **Починить freebuffd** | Жрёт 40% RAM, load 10+ | freebuff |
| 2 | **Централизовать ключи** | Токены и ключи в 7+ местах | Все |
| 3 | **Единый LLM Gateway** | 5 независимых реализаций | Leviathan + Triton + den4ik + Pelleto |
| 4 | **ADR-002 Core Kernel** | Фундамент для всего | Leviathan |
| 5 | **Agent Registry** | Управление агентами из единого центра | Все агенты |

### P1: Базовые сервисы (следующая неделя)

| # | Что | Почему | Системы |
|---|-----|--------|---------|
| 6 | **Централизованное логирование** | Нет единого log-агрегатора | Все |
| 7 | **Единая система авторизации** | Каждый сервис сам по себе | Все HTTP |
| 8 | **Мониторинг и алерты** | Load 10+ никто не заметил | Система |
| 9 | **Memory System глобальная** | 5 типов памяти для всех агентов | Все агенты |
| 10 | **Обёртка агентов в BaseRole** | Triton, den4ik, Arbitr → единый интерфейс | Все агенты |

### P2: Улучшения (2-3 неделя)

| # | Что | Почему | Системы |
|---|-----|--------|---------|
| 11 | **Декомпозиция den4ik-claude** | 125KB монолит → модули | den4ik |
| 12 | **Postgres для агентов** | Вместо 20+ SQLite | Все |
| 13 | **Распределённое кэширование** | Redis для всех | Все |
| 14 | **Freebuff как аудит-слой** | А не отдельный демон | freebuff |
| 15 | **CI/CD для деплоя** | git push → авто-деплой | Leviathan |

### P3: Оптимизация (3-4 неделя)

| # | Что | Почему | Системы |
|---|-----|--------|---------|
| 16 | **Docker-контейнеризация** | Изоляция, масштабирование | Leviathan, Triton |
| 17 | **Performance: RAM/CPU** | 88% swap — это критично | Все |
| 18 | **Тесты (≥70%)** | 0 тестов в Leviathan | Leviathan |
| 19 | **Документация** | ARCHITECTURE, API, CLI | Все |

### P4: Будущее

| # | Что | Системы |
|---|-----|---------|
| 20 | Plugin SDK (hot-plug агенты) | LAP |
| 21 | Multi-Agent Coordination (AutoGen-style) | LAP |
| 22 | Sandbox (Docker изоляция кода) | LAP |
| 23 | Health Dashboard | Все |

---

## 4. 🏛️ ЦЕЛЕВАЯ АРХИТЕКТУРА: ЕДИНЫЙ ОРГАНИЗМ

### 4.1 Концепция

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        LEVIATHAN ECOSYSTEM                             │
│                    Единый организм из 5 слоёв                           │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│  СЛОЙ 1: ИНТЕРФЕЙСЫ (Единые точки входа)                              │
│                                                                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐                │
│  │  CLI     │  │  HTTP    │  │   MCP    │  │ Telegram │                │
│  │  lev-cli │  │  :8200   │  │  :8300   │  │  @bot    │                │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘                │
│       │              │             │              │                      │
│       └──────────────┴─────────────┴──────────────┘                      │
└──────────────────────────┬──────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────────────┐
│  СЛОЙ 2: API GATEWAY (Единый шлюз)                                     │
│                                                                          │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  ┌────────────────┐  │
│  │  Auth       │  │  Rate Limit │  │  Router    │  │  Logging       │  │
│  │  (JWT/OAuth)│  │  (Redis)    │  │  Agent→Svc │  │  (центральное) │  │
│  └─────────────┘  └─────────────┘  └────────────┘  └────────────────┘  │
└──────────────────────────┬──────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────────────┐
│  СЛОЙ 3: ADR-002 KERNEL (Мозг)                                         │
│                                                                          │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │  DecisionEngine → RoleSelector → ExecutionLoop → Replanner     │     │
│  │  SafetyGuards (max_steps, max_cost, timeout, circuit_breaker)  │     │
│  └────────────────────────────────────────────────────────────────┘     │
│                           │                                             │
│  ┌────────────────────────────────────────────────────────────────┐     │
│  │  AgentRegistry (все агенты зарегистрированы)                    │     │
│  │  ┌──────────┐ ┌──────────┐ ┌────────┐ ┌──────────┐ ┌────────┐ │     │
│  │  │ General  │ │  Sport   │ │Arbitr  │ │  TG      │ │ Voice  │ │     │
│  │  │ (core)   │ │(Triton)  │ │Cockpit │ │  Handler │ │  Bot   │ │     │
│  │  └──────────┘ └──────────┘ └────────┘ └──────────┘ └────────┘ │     │
│  └────────────────────────────────────────────────────────────────┘     │
└──────────────────────────┬──────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────────────┐
│  СЛОЙ 4: SHARED SERVICES (Органы)                                       │
│                                                                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌─────────┐  │
│  │  LLM     │  │ Memory   │  │ Logging  │  │ Secrets  │  │  Event  │  │
│  │  Gateway  │  │  System  │  │  System  │  │  Vault   │  │  Bus    │  │
│  │  unified │  │  V2      │  │  central │  │  AES-256 │  │ Dur+DLQ │  │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘  └─────────┘  │
│                                                                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐               │
│  │  Cache   │  │ Database │  │ Monitor  │  │  Auth    │               │
│  │  (Redis) │  │ (PG+SQL) │  │ +Alert   │  │  Service │               │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘               │
└──────────────────────────┬──────────────────────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────────────────────┐
│  СЛОЙ 5: INFRASTRUCTURE (Скелет)                                        │
│                                                                          │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐               │
│  │  nginx   │  │ Postgres │  │  Redis   │  │ systemd  │               │
│  │  reverse │  │  :5434   │  │  :6379   │  │  сервисы │               │
│  │  proxy   │  │          │  │          │  │          │               │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘               │
└─────────────────────────────────────────────────────────────────────────┘
```

### 4.2 Ключевые решения

#### Решение 1: Единый LLM Gateway
**Проблема:** 5 независимых реализаций LLM-провайдеров
**Решение:** Объединить `providers.py` + `ProductionLLMGateway` (Triton) + встроенный den4ik → единый `LLMGateway`
**Архитектура:**
```python
class LLMGateway:
    PROVIDERS = ProviderFactory(...)  # Gemini, Groq, Claude, DeepSeek
    ROUTER = ModelRouter(...)         # cost-aware, latency-aware
    CIRCUIT = CircuitBreaker(...)     # из Triton
    COST = CostManager(...)           # budget enforcement
```
**Влияет на:** Leviathan + Triton + den4ik + Pelleto

#### Решение 2: Централизованное логирование
**Проблема:** Логи разбросаны по 10+ местам, нет поиска, нет алертов
**Решение:** Все логи → единый pipeline: `JSON Log → Redis/Loki → аггрегация`
**Схема:**
```
[Приложение] → JSON Log → [Redis Stream / File] → [Log Aggregator]
                                                       ├── search (grep/лoki)
                                                       ├── alerts (telegram)
                                                       └── metrics (grafana)
```
**Влияет на:** Все сервисы

#### Решение 3: Централизованное хранение ключей
**Проблема:** Токены и ключи в 7+ .env файлах
**Решение:** Все ключи → Secrets Vault (:8400), .env → только VAULT_TOKEN
**Процесс:**
1. Проверить, что в Vault есть все ключи из всех .env
2. Заменить .env на ссылки на Vault
3. Добавить auto-rotation для токенов с истечением
**Влияет на:** Leviathan, Triton, den4ik, Pelleto, Arbitr

#### Решение 4: Unified Agent Interface (BaseRole)
**Проблема:** Каждый агент имеет свой интерфейс
**Решение:** Все агенты → `BaseRole(Runnable)` + `ScenarioPolicy(YAML)`
**Схема:**
```python
class SportObserverRole(BaseRole):
    """Бывший Triton Agent"""
    agent_id = "sport-observer"
    capabilities = ["sport_observation", "football"]
    
    async def execute(self, policy: ScenarioPolicy, state: ExecutionState):
        return await self.orchestrator.run(...)  # старый Triton код

class TelegramHandlerRole(BaseRole):
    """Бывший den4ik-claude"""
    agent_id = "tg-handler"
    capabilities = ["telegram", "messaging"]
    
    async def execute(self, policy: ScenarioPolicy, state: ExecutionState):
        return await self.bot_adapter.handle(...)
```

#### Решение 5: Единая система авторизации
**Проблема:** Каждый HTTP-сервис имеет свою аутентификацию (или не имеет)
**Решение:** nginx → Auth Service (JWT) → Backend
```
[Client] → nginx → [Auth: JWT/OAuth] → Backend Service
                          │
                    [Redis: sessions]
```

---

## 5. 📋 ДЕТАЛЬНЫЙ ПЛАН РЕАЛИЗАЦИИ

### Фаза 0: Экстренное спасение (1-2 дня)

**Цель:** Стабилизировать систему, остановить кровотечение ресурсов

```
День 1: 
├── 0.1 🔴 Починить freebuffd
│   ├── journalctl -u freebuffd -n 50 — диагностика
│   ├── Исправить ошибку (скорее всего интеграция с free-claude-code)
│   └── Перезапустить или отключить до лучших времён
│
├── 0.2 🟡 Собрать ВСЕ ключи в Vault
│   ├── Сканировать все .env файлы
│   ├── Загрузить в /opt/secrets_vault/
│   └── Проверить curl 127.0.0.1:8400/health
│
├── 0.3 🟡 Убрать мёртвые сервисы из systemd
│   └── citrus_aura, lawu, arbitr (дубликаты), agent-hub
│
└── 0.4 🟡 Остановить freebuffd если не чинится
    └── systemctl stop freebuffd && systemctl disable freebuffd
```

### Фаза 1: Ядро (3-5 дней)

**Цель:** Построить фундамент ADR-002 + AgentRegistry

```
День 2-3: ADR-002 Core Kernel
├── 1.1 core/scenarios/loader.py — ScenarioPolicy (Pydantic)
├── 1.2 core/agent/kernel.py — AgentKernel
├── 1.3 core/agent/decision_engine.py — DecisionEngine
├── 1.4 core/agent/execution_loop.py — ExecutionLoop
└── 1.5 core/safety/guards.py — SafetyGuards

День 4-5: Agent Registry
├── 1.6 core/roles/base.py — BaseRole(Runnable)
├── 1.7 core/roles/registry.py — AgentRegistry (hot-plug)
├── 1.8 core/roles/general_role.py — обёртка LeviathanAgent
└── 1.9 Тест: kernel + registry работают

День 6: Unified LLM Gateway
├── 1.10 lap/shared/llm_gateway.py — слияние providers + LLMGateway
├── 1.11 lap/shared/cost_manager.py — budget enforcement
└── 1.12 Замена старых вызовов на gateway
```

### Фаза 2: Сервисы (5-7 дней)

**Цель:** Построить единую инфраструктуру

```
День 7-8: Логирование
├── 2.1 Определить единый формат: JSON {timestamp, service, level, message, correlation_id}
├── 2.2 Обернуть существующие логи (systemd journal → JSON)
├── 2.3 Создать LogAggregator (чтение из всех источников)
├── 2.4 Интеграция с EventBus: логировать все события
└── 2.5 Alert на ошибки (TG notification при CRITICAL)

День 9-10: Хранение ключей
├── 2.6 Миграция всех .env → Secrets Vault
├── 2.7 Auto-rotation для TG_BOT_TOKEN и GITHUB_TOKEN
├── 2.8 Проверка: ни один .env не содержит живых ключей
└── 2.9 Документация: как добавить новый ключ

День 11-12: Авторизация + мониторинг
├── 2.10 Auth Service на nginx (JWT для всех HTTP)
├── 2.11 Health endpoint для каждого сервиса
├── 2.12 Мониторинг: load, RAM, swap, disk → алерт в TG
└── 2.13 Dashboard: systemd status всех сервисов
```

### Фаза 3: Миграция агентов (5-7 дней)

**Цель:** Все агенты под единым управлением

```
День 13-15: Агенты → BaseRole
├── 3.1 Triton → SportObserverRole (1 день)
│   ├── Обёртка ContentOrchestrator в BaseRole
│   └── CircuitBreaker → shared services
│
├── 3.2 ArbitrCockpit → ArbitrRole (0.5 дня)
│   └── Обёртка FastAPI в BaseRole
│
├── 3.3 den4ik-claude → TelegramHandlerRole (2 дня)
│   ├── Разделение bot.py на модули:
│   │   ├── handler_message.py
│   │   ├── handler_command.py
│   │   ├── session_manager.py
│   │   └── llm_adapter.py → LLM Gateway
│   └── Исходный bot.py — резервная копия
│
├── 3.4 Pelleto → PelletoRole (1 день)
│   ├── Починить MCP: pip install mcp fastmcp
│   └── KnowledgeBase → Memory System
│
└── 3.5 Vocal bot, Media Factory → Roles (0.5 дня каждый)

День 16-17: Интеграция
├── 3.6 Единый CLI: lev-cli run scenario X
├── 3.7 Единый MCP: :8300 — все инструменты всех агентов
├── 3.8 Единый TG: один бот роутит к разным агентам
└── 3.9 Единый API: :8200 — OpenAI-compatible
```

### Фаза 4: Production (3-5 дней)

**Цель:** Надёжность, тесты, документация

```
День 18-19: Тесты
├── 4.1 Unit: DecisionEngine, ExecutionLoop, SafetyGuards (≥80%)
├── 4.2 Integration: full loop, replan, safety violation
└── 4.3 Performance: decision <2s, checkpoint <100ms, memory <500MB

День 20-21: Документация
├── 4.4 ARCHITECTURE.md — полная схема
├── 4.5 API.md — все эндпоинты
├── 4.6 CLI_GUIDE.md — все команды
├── 4.7 SCENARIO_SPEC.md — как писать YAML-сценарии
└── 4.8 DEPLOY.md — как развернуть

День 22: Cleanup
├── 4.9 Удалить .bak файлы (1.5MB мусора)
├── 4.10 Слить ветку feature/claude-multi-account → main
├── 4.11 Отключить старые сервисы (citrus_aura, lawu)
├── 4.12 Настроить авто-бэкапы (диск 80% → нужна архивация)
└── 4.13 Очистить старые логи (>30 дней)
```

---

## 6. 📊 ДЕТАЛЬНЫЙ ПЛАН ПО ПОДСИСТЕМАМ

### 6.1 Система логирования

**Текущее состояние:** Логи в 10+ местах, нет централизации

**Проект решения:**

```
[Service] → JSON (syslog) → [Redis Stream / File]
                                    ↓
                           [LogProcessor]
                            ├── parse JSON
                            ├── enrich (correlation_id, service)
                            └── store (SQLite / Loki)
                                    ↓
                              [LogViewer]
                            ├── grep / fzf
                            ├── лента событий в TG
                            └── алерты при CRITICAL/ERROR
```

**Формат лога:**
```json
{
  "timestamp": "2026-07-02T10:00:00Z",
  "service": "leviathan-agent",
  "level": "INFO",
  "message": "Task started",
  "correlation_id": "task_abc123",
  "agent_id": "sport-observer",
  "latency_ms": 150,
  "extra": {}
}
```

**План:**
1. Все Python-сервисы → единый `leviathan_logger` (логгер из `agent/providers` пишет JSON)
2. Все shell-скрипты → `logger.sh` обёртка
3. LogProcessor — читает из journal и файлов, пишет в единое хранилище
4. Web UI для просмотра логов

### 6.2 Хранение ключей

**Текущее состояние:** 7+ .env файлов с живыми ключами

**Проект решения:**

```
Secrets Vault (:8400) — уже работает! Нужно:
├── 1. Загрузить все ключи из всех .env
├── 2. Удалить ключи из .env (оставить только VAULT_TOKEN)
├── 3. Auto-rotation для TG_BOT_TOKEN
└── 4. Доступ через API: curl -s 127.0.0.1:8400/secret/TG_BOT_TOKEN
```

**Миграция:**
```
До:  /opt/leviathan_engine/agent_service/.env → TG_BOT_TOKEN=86046...
После: /opt/leviathan_engine/agent_service/.env → VAULT_TOKEN=xxx (только он)
       TG_BOT_TOKEN → vault set TG_BOT_TOKEN 86046...
       Код → vault.get("TG_BOT_TOKEN")
```

### 6.3 Ротация LLM

**Текущее состояние:** ModelRouter есть, но:
- Gemini key rotation на 429 (базовый)
- Нет budget enforcement
- Нет cost tracking

**Проект решения:**

```python
class LLMRouter:
    # Политики ротации:
    POLICIES = {
        "cost_optimized": {
            "fallback_chain": ["gemini", "groq", "claude"],
            "budget_usd": 0.50,          # на задачу
            "daily_budget_usd": 10.0,    # на день
            "mode": "FLASH",             # режим ADR-002
        },
        "quality": {
            "fallback_chain": ["claude", "gemini", "groq"],
            "budget_usd": 5.0,
            "mode": "PRO",
        },
        "balanced": {
            "fallback_chain": ["gemini", "groq"],
            "budget_usd": 2.0,
            "mode": "AUTO",
        }
    }
    
    async def route(self, task, policy="balanced") -> ProviderResponse:
        for provider in self.POLICIES[policy]["fallback_chain"]:
            if self.cost_manager.can_afford(provider, task):
                try:
                    return await provider.call(task)
                except (RateLimitError, QuotaExceeded):
                    continue  # fallback к следующему
        raise AllProvidersExhausted()
```

**План:**
1. Интегрировать cost tracking в ModelRouter
2. Gemini key pool → auto-rotation c quota monitoring
3. Groq → добавить ключи (сейчас только 1)
4. Claude → bridge через Claude adapter
5. Добавить DeepSeek как бюджетный fallback

### 6.4 Agent Registry

**Текущее состояние:** 5+ независимых агентов

**Проект решения:**

```python
class AgentRegistry:
    """Единый реестр агентов"""
    
    def __init__(self):
        self._agents: Dict[str, BaseRole] = {}
        self._capabilities: Dict[str, List[str]] = {}
    
    def register(self, agent: BaseRole):
        """Регистрация агента (hot-plug)"""
        self._agents[agent.agent_id] = agent
        self._capabilities[agent.agent_id] = agent.capabilities
    
    def route(self, task: AgentTask) -> BaseRole:
        """Авто-роутинг задачи к подходящему агенту"""
        # 1. Intent Detection (из agent/intent.py)
        intent = self.detect_intent(task.prompt)
        
        # 2. Поиск по capabilities
        candidates = [
            aid for aid, caps in self._capabilities.items()
            if intent.requires in caps
        ]
        
        # 3. Если несколько — DecisionEngine
        if len(candidates) > 1:
            return self.decision_engine.select(candidates, task)
        
        return self._agents[candidates[0]]
    
    async def health_all(self) -> Dict[str, Status]:
        """Проверка всех агентов"""
        return {
            aid: await agent.health_check()
            for aid, agent in self._agents.items()
        }
```

### 6.5 Базы данных

**Текущее состояние:** 20+ SQLite БД, 1 Postgres

**Проект решения:**

```
Этап 1: Стандартизация
├── Все SQLite → /data/leviathan/<db_name>.db
├── Единая миграционная схема (alembic-like)
└── Бэкапы: pg_dump + sqlite3 .dump → /backups/

Этап 2: Централизация (опционально)
├── Postgres: единая БД для всех агентов
├── Redis: кэш + очереди + session store
└── SQLite: только для локальных данных (логи, кэш)
```

---

## 7. 🗓️ ТАЙМЛАЙН (календарь)

```
Неделя 1 (Фаза 0-1):
├── Пн: Починить freebuffd, собрать ключи
├── Вт: ADR-002 Kernel (ScenarioPolicy + DecisionEngine)
├── Ср: ADR-002 Kernel (ExecutionLoop + SafetyGuards)
├── Чт: Agent Registry (BaseRole + регистрация агентов)
├── Пт: Unified LLM Gateway
└── Сб: Тестирование ядра

Неделя 2 (Фаза 2):
├── Пн: Централизованное логирование
├── Вт: Миграция ключей в Vault
├── Ср: Auth Service + health monitoring
├── Чт: Monitor dashboard + TG alerts
└── Пт: Интеграционное тестирование

Неделя 3 (Фаза 3):
├── Пн: Triton → SportObserverRole
├── Вт: Arbitr → ArbitrRole
├── Ср-Чт: den4ik-claude → TelegramHandlerRole (декомпозиция)
├── Пт: Pelleto → PelletoRole
└── Сб: Единые интерфейсы (CLI, API, MCP, TG)

Неделя 4 (Фаза 4):
├── Пн-Вт: Тесты (unit + integration)
├── Ср-Чт: Документация
└── Пт: Cleanup + merge веток
```

---

## 8. ⚡ ЧТО МОЖНО СДЕЛАТЬ ПРЯМО СЕЙЧАС

```bash
# 1. Диагностика freebuffd
journalctl -u freebuffd --no-pager -n 50

# 2. Проверка свободного места
du -sh /opt/*/ 2>/dev/null | sort -rh | head -20

# 3. Сбор ключей в Vault
# (собрать все .env → проверить Vault → загрузить недостающие)

# 4. Создание структуры ADR-002
mkdir -p /opt/leviathan_engine/agent_service/core/{scenarios,agent,roles,safety,artifacts}
touch /opt/leviathan_engine/agent_service/core/__init__.py
```

---

## 9. 📎 ЧЕКЛИСТ ГОТОВНОСТИ

### 🔴 Блокеры (решить до начала)

- [ ] freebuffd — диагностирован и исправлен или отключён
- [ ] Ключи — собраны и загружены в Vault
- [ ] Диск — освобождено минимум 5GB
- [ ] Swap — снижен до <50%

### ✅ Критерии готовности Фазы 1

- [ ] `ScenarioPolicy` загружается из YAML
- [ ] `DecisionEngine` выбирает роль по задаче
- [ ] `ExecutionLoop` делает observe→decide→act→eval
- [ ] `BaseRole` обёртка для GeneralAgent работает
- [ ] `LLMGateway` объединяет всех провайдеров

### ✅ Критерии готовности Фазы 2

- [ ] Все логи пишутся в едином JSON-формате
- [ ] Все ключи только в Vault
- [ ] Health endpoint у каждого сервиса
- [ ] Алерты при падении сервиса

### ✅ Критерии готовности Фазы 3

- [ ] Triton, Arbitr, den4ik-claude, Pelleto — все как BaseRole
- [ ] `lev-cli run scenario X` запускает любого агента
- [ ] Единый MCP (:8300) со всеми инструментами
- [ ] Единый TG-бот для всех агентов

### ✅ Критерии готовности Фазы 4

- [ ] Тесты ≥70%
- [ ] Документация: ARCHITECTURE, API, CLI, SCENARIO, DEPLOY
- [ ] Чистый git: main — source of truth
- [ ] Авто-бэкапы, старые логи удалены

---

## 10. 📝 ЗАКЛЮЧЕНИЕ

Сервер LEVIATHAN представляет собой **богатую экосистему** из 30+ проектов, 20+ активных сервисов и глубокой интеграции с LLM (Gemini, Groq, Claude). Проблема не в отсутствии компонентов, а в **отсутствии связности**.

**Ключевые метрики:**
- 4/10 — архитектурная целостность
- 2/10 — unified infrastructure (logging, keys, auth)
- 6/10 — LLM capabilities (хороший набор, плохая интеграция)
- 3/10 — operational readiness (нет мониторинга, тестов)

**План** — не переписывание, а постепенная **унификация**:
- ADR-002 даёт единый мозг (kernel)
- Shared Services дают единые органы (logging, keys, gateway)
- BaseRole даёт единый язык для всех агентов
- Фазы 0→4 дают безопасный путь без остановки production

---

*Документ создан: 2026-07-02 | Полный аудит сервера LEVIATHAN*
*Составлено на основе: systemctl, ss, ps, find, git, cat — 30+ команд*
