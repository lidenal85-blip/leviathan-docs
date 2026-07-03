# 💻 ПОЛНЫЙ СИСТЕМНЫЙ АУДИТ LEVIATHAN (ВСЕ ДИРЕКТОРИИ)

**Дата:** 2026-07-03
**Сервер:** LEVIATHAN (78.17.24.96, Ubuntu 24.04, 4 ядра, 3.8 GB RAM)
**Диск:** /dev/vda1, 30GB ext4, 81% used

---

## 1. ТОПОЛОГИЯ ДИСКА

### Разделы
```
NAME    SIZE TYPE MOUNTPOINT        FSTYPE
vda      30G disk                    
└─vda1   30G part /                 ext4
loop0  63.8M loop /snap/core20/2866 squashfs
loop1  49.3M loop /snap/snapd/26865 squashfs
```

### Расход диска по корневым директориям
| Директория | Размер | % от диска |
|-----------|--------|-----------|
| /usr      | 9.4 GB | 31% |
| /root     | 3.0 GB | 10% |
| /opt      | 2.3 GB | 8% |
| /var      | 2.0 GB | 7% |
| /snap     | 358 MB | 1% |
| /tmp      | 211 MB | <1% |
| /boot     | 200 MB | <1% |

### Swap файлы (всего 5.5 GB swap)
| Файл | Размер | Использование |
|------|--------|--------------|
| /swapfile_new | 3.0 GB | Active (819 MB used) |
| /swapfile2g | 2.0 GB | Active (1.3 GB used) |
| /swapfile | 512 MB | Active |

---

## 2. ДИРЕКТОРИЯ /root/ (3.0 GB)

### 2.1 Проекты в /root/

| Проект | Размер | Файлов | README | Описание |
|--------|--------|--------|--------|----------|
| **ai-interior-composer** | 391 MB | 26,547 | ✅ | Дизайн интерьера с ИИ (Production-ready v1.0.0-P0) |
| **free-claude-code** | 193 MB | 7,761 | ✅ | Инструменты Claude Code CLI, VS Code |
| **Claude** | 46 MB | 324 | ❌ | Репорты и артефакты Claude |
| **backups** | 17 MB | 136 | ❌ | Бекапы |
| **survey-finder** | 4.1 MB | 622 | ✅ | Система мониторинга опросов |
| **core** | 2.6 MB | 32 | ❌ | — |
| **analysis** | 784 KB | 31 | ❌ | Аналитические отчёты |
| **tszh-bot** | 636 KB | 59 | ❌ | Telegram бот ТСЖ |
| **leviathan-property** | 208 KB | 45 | ❌ | — |
| **bin** | 196 KB | 23 | ❌ | Пользовательские скрипты |
| **freebuff** | 152 KB | 11 | ❌ | Freebuff CLI |
| **registry** | 120 KB | 15 | ❌ | — |
| **cert** | 16 KB | 2 | ❌ | Сертификаты |
| **snap** | 4 KB | 0 | ❌ | Snap ссылки |

### 2.2 Скрытые директории /root/

| Директория | Размер | Назначение |
|-----------|--------|------------|
| /root/.local | 553 MB | Локальные данные (uv, pip) |
| /root/.config | 561 MB | Конфиги (manicode, codebuff) |
| /root/.cache | 578 MB | Кэш |
| /root/.u2net | 168 MB | ML модель (u2net.onnx) |
| /root/.claude | 164 MB | Claude Code данные |
| /root/.kimi-code | 152 MB | Kimi Code CLI |
| /root/.npm | 77 MB | NPM кэш |
| /root/.ssh | 68 KB | SSH ключи |
| /root/.gnupg | 16 KB | GPG ключи |
| /root/.docker | 820 KB | Docker конфиги |
| /root/.bash_history | 72 KB | 2000 команд истории |

### 2.3 SSH ключи (/root/.ssh/)

**Ключи:**
- `id_ed25519` / `id_ed25519.pub`
- `levi_key` / `levi_key.pub`
- `termux_rsa` / `termux_rsa.pub`
- `authorized_keys` — список разрешённых ключей
- `config`, `config.bak` — SSH конфигурация

### 2.4 Файлы в корне /root/

**Скрипты (.sh):**
- `audit_kinovibe.sh`, `fcc-setup.sh`, `leviathan_dump.sh`
- `run_claude_autonomous.sh`, `setup_freelance.sh`
- `check_gemini_keys.sh`, `backup_leviathan.sh`
- `audit_leviathan.sh`, `send_docs.sh`, `dump_project.sh`
- `push_all_to_github.sh`

**Python (.py):**
- `gemini_proxy.py` — прокси для Gemini API
- `fix_mcp_oauth.py` — фикс OAuth для MCP

**Конфиги:**
- `.env` — переменные окружения
- `.claude.json` — конфиг Claude Code
- `settings.json` — настройки
- `mkdocs.yml` — конфиг MkDocs

**Архивы:**
- `claude-backup.tar.gz` (39 MB)
- `f3platform.tar.gz` (62 KB)
- `tools.tar.gz` (33 KB)

---

## 3. ДИРЕКТОРИЯ /usr/ (9.4 GB)

### 3.1 /usr/local/ — пользовательские установки

| Директория | Размер | Назначение |
|-----------|--------|------------|
| /usr/local/lib/python3.12/ | 2.5 GB | Python библиотеки |
| /usr/local/lib/ | 2.5 GB (всего) | Библиотеки |
| /usr/local/x-ui/ | 238 MB | X-UI панель управления |
| /usr/local/bin/ | 143 MB | Исполняемые файлы |

### 3.2 Ключевые бинарники в /usr/local/bin/

| Файл | Размер | Назначение |
|------|--------|------------|
| cloudflared | 39 MB | Cloudflare Tunnel |
| filebrowser | 36 MB | Файловый менеджер |
| dart | symlink → /opt/flutter/bin/dart | Dart SDK |
| alembic, arq, fastapi | — | Python утилиты |
| ct2-*, deep-translator, edge-tts | — | Конвертация/перевод/TTS |

### 3.3 Пакетные менеджеры

**Snap:** core20, snapd — минимальные системные
**NPM глобально:** codebuff, cline, llm-checker, claude-code, openai-codex, tsx, playwright-core, localtunnel
**Pip глобально:** aiohttp, aiogram, APScheduler, anthropic, asyncpg, alembic, beautifulsoup4, bcrypt

---

## 4. ДИРЕКТОРИЯ /etc/ — КОНФИГУРАЦИЯ

### 4.1 Nginx — все сайты

| Домен/Конфиг | Порт | Прокси/Корень | Назначение |
|-------------|------|--------------|------------|
| fashion-stylist | 80 | → 127.0.0.1:8555 | AI Fashion Stylist |
| f3platform | 8152 | /var/www/f3platform/frontend | F3 Platform |
| fcc-proxy | 8082 | → 127.0.0.1:8084 (auth) | FCC Proxy |
| fri-messenger | 8340 | → 127.0.0.1:3030 (WebSocket) | Messenger |
| leviathanstory | — | /var/www/leviathan_hub | Leviathan Hub |
| pelleto | 8082 | → :8082 + security headers | Pelleto AI |
| phonemanager | 8321 | → 127.0.0.1:8320 | Phone Manager |
| tszh-bot | 80 | → 127.0.0.1:8096 | ТСЖ бот |
| wgeasy | 8091 | → 127.0.0.1:51821 (WebSocket) | WireGuard Easy |
| ai-outreach | — | — | AI Outreach |
| kinovibe | — | — | KinoVibe |
| voidvpn | — | — | VoidVPN |

### 4.2 SSH конфигурация
- **Порт:** 22
- **Root login:** ✅ Разрешён
- **Password auth:** Нет (KbdInteractive: no)
- **X11 Forwarding:** Включён
- **ClientAliveInterval:** 15s
- **ClientAliveCountMax:** 3

### 4.3 Sudoers
- root, admin, sudo — полный доступ
- secure_path, env_reset, use_pty включены

### 4.4 System Environment
PATH: стандартный + /snap/bin

---

## 5. ДИРЕКТОРИЯ /var/ (2.0 GB)

### 5.1 Крупнейшие поддиректории

| Директория | Размер | Содержимое |
|-----------|--------|-----------|
| /var/log/journal/ | 768 MB | Systemd журналы |
| /var/www/kinovibe/ | 202 MB | Веб-проект KinoVibe |
| /var/lib/apt/ | 186 MB | APT кэш |
| /var/lib/snapd/ | 114 MB | Snap данные |
| /var/lib/postgresql/ | 79 MB | PostgreSQL 16 |
| /var/www/voicestudio/ | 64 MB | Voice Studio |
| /var/www/outreach/ | 50 MB | AI Outreach |
| /var/www/kinovibe_html/ | 24 MB | KinoVibe HTML |
| /var/www/kinovibe_sub/ | 23 MB | KinoVibe SUB |
| /var/www/leviathan_hub/ | 7.8 MB | Leviathan Hub |
| /var/log/nginx/ | 1.3 MB | Nginx логи |

### 5.2 Проекты в /var/www/

| Проект | Папка | Владелец |
|--------|-------|---------|
| KinoVibe | kinovibe, kinovibe_html, kinovibe_sub | claude_worker |
| Voice Studio | voicestudio, voice_studio | root |
| AI Outreach | outreach | root |
| F3 Platform | f3platform | root |
| HH Sniper | hh_sniper | root |
| Leviathan Hub | leviathan_hub | root |
| AIC | aic | root |
| Narrative | narrative | root |

### 5.3 Базы данных PostgreSQL

| База | Владелец |
|------|---------|
| aic_composer | aic |
| tg_monitor | tg_monitor |
| vocalplatform | vocaluser |
| postgres (шаблоны) | postgres |

### 5.4 Проблемные логи
- **Ошибка порта 8765:** Address already in use — повторяется в journalctl
- **vocal-gateway:** FastAPIError с UploadFile
- **vocal_bot:** ModuleNotFoundError: apscheduler

---

## 6. ДИРЕКТОРИЯ /home/ И ПОЛЬЗОВАТЕЛИ

### Пользователи с shell-доступом
| Пользователь | UID | Домашняя директория |
|-------------|-----|-------------------|
| root | 0 | /root (3.0 GB) |
| claude_worker | 1000 | /home/claude_worker (356 KB) |
| nova | 111 | — |
| postgres | 113 | /var/lib/postgresql |
| sync | 4 | — |

---

## 7. ДИРЕКТОРИЯ /opt/ (2.3 GB) — ПОЛНЫЙ СПИСОК

### Git репозитории с remote URL

| Проект | GitHub |
|--------|--------|
| leviathan_engine | github.com/den4ikorm1985/leviathan-engine.git |
| arbitr_cockpit | github.com/den4ikorm1985/arbitr-cockpit.git |
| den4ik-claude | github.com/den4ikorm1985/den4ik-claude.git |
| diet_platform | github.com/lidenal85-blip/diet_platform.git |
| pelleto-ai | github.com/den4ikorm1985/pelleto-ai.git |
| triton_agent | (в составе engine?) |
| savdabot | github.com/lewinthinkman-wq/SavdaBot.git |
| vocal_bot | github.com/den4ikorm1985/vocal-bot.git |
| citrus_aura | github.com/den4ikorm1985/citrus-aura.git |
| fri-messenger | github.com/den4ikorm1985/fri-messenger.git |
| kwork_bot | github.com/den4ikorm1985/kwork-bot.git |
| kwork_dispatcher | github.com/den4ikorm1985/kwork-dispatcher.git |
| media_factory | github.com/den4ikorm1985/media-factory.git |
| agrobot | github.com/den4ikorm1985/agrobot.git |
| secrets_vault | github.com/lidenal85-blip/secrets-vault.git |
| leviathan-docs | github.com/lidenal85-blip/leviathan-docs.git |
| agent_output | github.com/lidenal85-blip/agent_output |

---

## 8. ДИРЕКТОРИЯ /tmp/ (211 MB)

| Объект | Размер |
|--------|--------|
| /tmp/chroma_db_backup_*/ | 107 MB |
| /tmp/main-check/ | 7.4 MB |
| /tmp/main-audit/ | 7.4 MB |
| /tmp/leviathan-site/ | 3.4 MB |
| .so библиотеки | ~4.6 MB каждый |

---

## 9. ФАЙЛЫ НА КОРНЕВОМ УРОВНЕ / (НЕСТАНДАРТНЫЕ)

| Файл | Размер | Назначение |
|------|--------|------------|
| blizkie_ludy.tar.gz | 152 MB | Архив "Близкие Люди" |
| claude_docs.tar.gz | 167 MB | Claude документация |
| config.db | 128 KB | Конфигурация |
| settings.json | — | Настройки |
| run_tests.sh | — | Тестовый скрипт |

---

## 10. АНАЛИЗ БЕЗОПАСНОСТИ (ПОЛНЫЙ)

### 10.1 Секреты в git URL (в открытом виде)
В remote URL ряда репозиториев обнаружены токены аутентификации GitHub.

### 10.2 SSH доступ
- Root login разрешён — критическая уязвимость
- 3 пары SSH ключей (id_ed25519, levi_key, termux_rsa)
- authorized_keys настроен

### 10.3 GPG ключи
- Присутствуют в /root/.gnupg (16 KB)

### 10.4 Docker
- Docker установлен (конфиг в /root/.docker)
- 0 активных контейнеров
- Docker не используется

### 10.5 X-UI панель
- Установлена в /usr/local/x-ui/ (238 MB)
- Порты: 2096, 29585, 4443 (xray)
- Потенциально используется для прокси/VPN

---

## 11. АРХИТЕКТУРНАЯ КАРТА ВСЕЙ СИСТЕМЫ

```
/ (30GB ext4, 81% used)
├── /usr/ (9.4 GB) — системные программы
│   ├── /usr/local/ — пользовательские
│   │   ├── python3.12/ (2.5 GB)
│   │   ├── x-ui/ (238 MB) — X-UI панель
│   │   └── bin/ (143 MB) — cloudflared, filebrowser, dart
│   └── ...
├── /root/ (3.0 GB) — root home
│   ├── ai-interior-composer/ (391 MB)
│   ├── free-claude-code/ (193 MB)
│   ├── .config/ (561 MB), .cache/ (578 MB), .local/ (553 MB)
│   └── analysis/ — 17+ markdown документов
├── /opt/ (2.3 GB) — основные проекты
│   ├── leviathan_engine/ — ядро (25K LOC, 4 сервиса)
│   ├── vocalplatform/ — вокал (3 сервиса, проблемы)
│   ├── arbitr_cockpit/ — арбитраж
│   └── 15+ других проектов
├── /var/ (2.0 GB) — данные
│   ├── www/ — 12 веб-проектов
│   ├── lib/postgresql/ — 6 БД
│   └── log/journal/ — 768 MB логов
├── /home/ — claude_worker (356 KB)
├── /tmp/ — chroma_db backups (107 MB)
└── swap: 5.5 GB total (2.1 GB used)
```

---

## 12. ИТОГО: ПОЛНАЯ ИНВЕНТАРИЗАЦИЯ

**Всего проектов/сервисов на системе: ~40+**
- /opt/: 20+ проектов (15+ systemd сервисов)
- /root/: 13 проектов
- /var/www/: 12 веб-проектов
- /usr/local/: системные утилиты

**Базы данных:**
- PostgreSQL: 6 баз (aic_composer, tg_monitor, vocalplatform + templates)
- SQLite: ~10+ файлов (по одному в каждом Python проекте)
- ChromaDB: векторная БД (savdabot RAG)
- Redis: установлен (порт 6379)

**Пользователи системы:** root, claude_worker, nova, postgres

**GitHub аккаунты:**
- github.com/den4ikorm1985 (основной разработчик) — 15+ репозиториев
- github.com/lidenal85-blip (LEVIATHAN org) — несколько репозиториев
- github.com/lewinthinkman-wq (SavdaBot)

**Критические проблемы (дополнительно к архитектурному аудиту):**
1. Root login по SSH разрешён
2. Токены в git remote URL
3. Docker установлен, но не используется (лишний расход места)
4. X-UI панель — потенциальный вектор атаки
5. /tmp/ не очищается — 107 MB chroma_db backup
6. 3 swap файла вместо одного — неоптимально
7. /usr/local/lib/python3.12/ — 2.5 GB, потенциально раздуто
8. /var/log/journal/ — 768 MB, можно очистить

---

*Документ создан: 2026-07-03*
*Аудитор: Buffy (Codebuff AI)*
*Файл: /root/analysis/SYSTEM_AUDIT_2026-07-03.md*
