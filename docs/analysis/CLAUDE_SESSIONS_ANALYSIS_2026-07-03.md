# 🧠 ПОЛНЫЙ АНАЛИЗ СЕССИЙ CLAUDE CODE

**Дата:** 2026-07-03
**Источник:** /root/.claude/ (164 MB, 2206 файлов)

---

## 1. СТРУКТУРА /root/.claude/

| Директория | Размер | Файлов | Назначение |
|-----------|--------|--------|------------|
| **projects/** | ~128 MB | 9 проектов | Кэши контекстов проектов, сессии |
| **file-history/** | 26 MB | 1512 | Версионная история изменений файлов |
| **plugins/** | 5.8 MB | 376 | Плагины Claude Code |
| **uploads/** | 2.2 MB | 10 | Загруженные файлы |
| **paste-cache/** | 864 KB | 145 | Кэш вставок |
| **cache/** | 460 KB | 2 | Конфиг + ченджлог |
| **backups/** | 244 KB | 5 | Бэкапы .claude.json |
| **debug/** | 204 KB | 1 | Лог инициализации |
| **session-env/** | 188 KB | — | Метаданные сессий |
| **skills/** | 52 KB | 8 | Навык impeccable |
| **plans/** | 20 KB | 2 | Планы |
| **shell-snapshots/** | 12 KB | 1 | Снимки оболочки |

---

## 2. ПРОЕКТЫ В /root/.claude/projects/

### 2.1 -root (99 MB, 60 файлов) — ГЛАВНЫЙ

Полная история всех сессий Claude Code от root. 26 session.jsonl файлов.

**3 крупнейшие сессии:**

| Файл | Размер | Строк | Суть |
|------|--------|-------|------|
| `81d511b2...` | **40 MB** | 15,097 | Настройка векторной памяти FastEmbed для проекта Odysseus (Docker, sentence-transformers). Прерванные фоновые задачи. |
| `09f88a2e...` | **24 MB** | ~9,000 | Настройка SSH-доступа, bash-функций для удалённого управления, интеграция Google Drive. Прерванные фоны. |
| `68e2e0ed...` | **7.5 MB** | ~2,800 | Анализ заказа "Арбитр кокпита", оценка стоимости (Орионикс). Обсуждение связности сессий. |

**Файлы памяти -root:**

| Файл | Назначение |
|------|------------|
| **feedback-order-filter.md** | Правила отбора заказов: ✅ боты/парсеры/web/API/TMA. ❌ нативная мобильная разработка |
| **feedback-reports-format.md** | Отчёты строго в `.md`, отправка через Telegram Bot API в канал "Денискины отчёты" |
| **feedback-screenshot.md** | По команде "посмотри скриншот" — последний файл из `/storage/emulated/0/Pictures/Screenshots/` на `phone2` |
| **feedback-telegram-send.md** | Запрет на остановку `den4ik-claude.service`. Отправка через IPC-очередь или Bot API |
| **project-claude-system.md** | Системные правила: структура `/root/Claude/`, туннель на телефон `ssh termux-phone` |
| **project-sota-license.md** | Стратегия защиты PHP-кода "СОТА·I": ionCube, перенос платежей на свой сервер |
| **project-vocal-course.md** | Вокальный курс: боты @Soul_Vocal_Bot, @vocalclaude_22163_bot, интеграция VoiceStudio |

---

### 2.2 -var-www-kinovibe (23 MB, 39 файлов)

**Сессии:** 4 крупных session.jsonl (5.7M, 4.6M, 2.4M, 1.6M, 1.3M)

**Файлы памяти:**

#### MEMORY.md
Ссылка на основной контекст `project_kinovibe.md`

#### SESSION_PLANNING.md — полный план разработки:

**Блок 1 — Критические баги:**
- Исправление YouTube seek
- Устранение разрывов сессий при смене устройства
- Псевдо-полноэкранный режим с чатом поверх

**Блок 2 — 9 новых систем:**
- Король комнаты (ролевая система)
- Режим голосования (Демократия)
- Догоняющий плеер (catch-up)
- AI-озвучка субтитров (Edge-TTS)
- Лобби ожидания
- Наложение чата
- Зеркала TorrServer
- Валидатор торрентов
- Оптимизация TorrServer

---

### 2.3 -opt-pelleto-ai (1.4 MB, 6 файлов)

**Сессии:** 1 крупная (1.1 MB)

**Файлы памяти:**

#### project_mcp.md
- **MCP-сервер:** порт 8132, FastMCP, 20 инструментов
- **Интеграция:** Gemini через `call_gemini_with_tools`
- **Критично:** `starlette==0.41.3` для совместимости с FastAPI 0.115.5
- **Группы инструментов:** Termux/Linux, Android, Vibe Coding, Сеть/медиа, БД/мониторинг, Торренты

#### user_context.md
- **Инженер:** @vaalchik
- **Сервер:** 78.17.24.96
- **Стек:** FastAPI, Gemini, aiogram3, SQLite
- **Порты:** 8132 (MCP), 8200 (Leviathan), 8095 (ArbitrCockpit), 8110 (KinoVibe), 8120 (VoiceStudio)

---

### 2.4 -var-www-voice-studio (1.5 MB, 4 файла)

- 1 сессия: 1.3 MB
- MEMORY.md отсутствует

---

### 2.5 Прочие проекты

| Проект | Размер | Файлов | Комментарий |
|--------|--------|--------|-------------|
| -var-www-kinovibe-src | 2.4 MB | 9 | Исходники KinoVibe |
| -opt-leviathan-engine-агент-service | 988 KB | 6 | Ядро engine |
| -opt-hq | 36 KB | 2 | Leviathan HQ |
| -opt-night-research | 32 KB | 1 | Ночные исследования |
| -root-free-claude-code | 100 KB | 3 | Free Claude Code |

---

## 3. ПОЛНЫЙ ДАМП СЕССИЙ (66 .jsonl)

| # | Размер | Проект | Первые символы |
|---|--------|--------|---------------|
| 1 | **40 MB** | -root | Настройка FastEmbed для Odysseus |
| 2 | **24 MB** | -root | SSH + Google Drive |
| 3 | 7.5 MB | -root | Арбитр кокпит анализ |
| 4 | 5.7 MB | kinovibe | — |
| 5 | 4.6 MB | kinovibe | — |
| 6 | 2.5 MB | -root | — |
| 7 | 2.4 MB | kinovibe | — |
| 8 | 2.1 MB | -root | — |
| 9 | 1.9 MB | -root | Создание тг бота den4ik_claude |
| 10 | 1.6 MB | kinovibe | — |
| 11 | 1.3 MB | voice-studio | — |
| 12 | 1.3 MB | kinovibe | — |
| 13 | 1.1 MB | pelleto-ai | — |
| 14+ | <1 MB | все | Разные сессии |

**Всего:** 66 файлов session.jsonl, от 188 байт до 40 MB

---

## 4. КЛЮЧЕВЫЕ ВЫВОДЫ

### Технический стек Claude Code:
- **Модель:** `claude-sonnet-4-6` (основная)
- **Плагины:** Kotlin LSP, Ralph Loop, Learning Output Style, Code Simplifier, Playground, Clangd LSP
- **Навыки:** impeccable
- **MCP серверы:** lev_bash, lev_read_file и др.
- **Workflow:** ctx_restore → ctx_start → ctx_log_decision → ctx_checkpoint

### Проблемные паттерны:
1. **Прерванные фоновые задачи** — частая проблема в крупных сессиях (40MB, 24MB)
2. **Раздутие сессий** — 40MB и 24MB без явной причины такого объёма
3. **Нет связности между сессиями** — пользователь явно запрашивал параллельную работу
4. **Отсутствие MEMORY.md** в voice-studio и некоторых проектах

### Рабочие процессы:
- Telegram боты — основной интерфейс (den4ik-claude, vocal_bot, kwork_bot, savdabot)
- SSH туннель на телефон (`ssh termux-phone`) для скриншотов
- Все отчёты → Telegram в `.md`
- Заказы фильтруются: ❌ нативные приложения, ✅ веб/боты/API

---

*Документ создан: 2026-07-03*
*Файл: /root/analysis/CLAUDE_SESSIONS_ANALYSIS_2026-07-03.md*
