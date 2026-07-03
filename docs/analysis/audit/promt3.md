# 🎯 ПРОМТ: АУДИТ + СОЗДАНИЕ ИНСТРУМЕНТОВ + ДЕПЛОЙ

```markdown
# 🛠️ ПОЛНЫЙ ЦИКЛ: АУДИТ → ИНСТРУМЕНТЫ → ДЕПЛОЙ

## 🎯 ТВОЯ РОЛЬ

Ты — **Principal Platform Engineer**. У тебя есть **4 часа** на сервере LEVIATHAN для:
1. Проведения полного аудита всех проектов (60+)
2. Определения каких инструментов не хватает
3. Создания этих инструментов
4. Деплоя всего в GitHub + GitHub Pages

---

## 📍 КОНТЕКСТ

**Сервер:** LEVIATHAN (78.17.24.96, Ubuntu 24.04)  
**Рабочая папка:** `/opt/leviathan-docs/`  
**SSH alias:** `lev` (уже настроен, без пароля)  
**GitHub:** https://github.com/lidenal85-blip/leviathan-docs  
**Сайт:** https://lidenal85-blip.github.io/leviathan-docs/  
**Ветка деплоя:** `gh-pages`  
**MkDocs venv:** `/opt/mkdocs-venv/`  
**Документация:** `/root/analysis/` (30+ файлов)

**Текущее состояние:** Сайт уже работает, есть базовая документация. Но **нет инструментов** для управления платформой.

---

## 📋 СТРУКТУРА РАБОТЫ (4 фазы)

### ФАЗА 1: АУДИТ (60 минут)

Найди **ВСЕ** проекты на сервере (не только в `/opt/`, но и в `/root/`, `/srv/`, Docker, systemd, cron, pm2 и т.д.).

**Используй команды:**
```bash
# Все директории
ls -la /opt/ /root/ /home/*/ /srv/ /var/www/ 2>/dev/null

# Все git-репозитории
find / -name ".git" -type d 2>/dev/null | grep -v "/proc\|/sys\|/dev"

# Все маркеры проектов
find / -type f \( -name "package.json" -o -name "requirements.txt" -o -name "pyproject.toml" -o -name "go.mod" -o -name "Dockerfile" -o -name "docker-compose.yml" \) 2>/dev/null | grep -v "/proc\|/sys\|/usr/lib\|node_modules"

# Все запущенные сервисы
systemctl list-units --type=service --state=running
docker ps -a
pm2 list 2>/dev/null
crontab -l
ps aux | grep -E "(python|node|go|java)" | grep -v grep
```

**Результат фазы 1:**
```
/root/analysis/audit/PROJECT_LIST.md       # Список всех 60+ проектов
/root/analysis/audit/*/audit.md            # Детальный аудит каждого
/root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md  # Главный summary
```

---

### ФАЗА 2: GAP-АНАЛИЗ ИНСТРУМЕНТАРИЯ (30 минут)

Проанализируй чего **НЕ ХВАТАЕТ** для управления платформой:

#### Проверь существующее:
```bash
which lev-cli lev-status lev-audit lev-backup lev-monitor 2>/dev/null
ls /usr/local/bin/lev* 2>/dev/null
ls /opt/leviathan-docs/tools/ 2>/dev/null
ls /opt/leviathan-docs/scripts/ 2>/dev/null
```

#### Определи gaps по категориям:

**1. Мониторинг и observability**
- Есть ли единая команда для проверки здоровья всех сервисов?
- Есть ли dashboard с метриками?
- Есть ли алерты?

**2. Управление проектами**
- Можно ли одной командой запустить/остановить проект?
- Есть ли команда для обновления всех проектов?
- Есть ли команда для бэкапа?

**3. Безопасность**
- Есть ли сканер секретов?
- Есть ли проверка CVE в зависимостях?
- Есть ли аудит прав доступа?

**4. Производительность**
- Есть ли анализ использования ресурсов по проектам?
- Есть ли поиск bottleneck'ов?

**5. Деплой и CI/CD**
- Есть ли автоматический деплой?
- Есть ли rollback?

**6. Документация**
- Генерируется ли документация автоматически?
- Есть ли поиск по документации?

**Результат фазы 2:**
```
/root/analysis/audit/TOOLING_GAP_ANALYSIS.md
```

Структура:
```markdown
# 🔧 GAP-АНАЛИЗ ИНСТРУМЕНТАРИЯ

## Нехватка критичная (🔴)
1. [Инструмент 1] — зачем нужен
2. [Инструмент 2] — зачем нужен

## Нехватка важная (🟡)
1. [Инструмент 1] — зачем нужен

## Нехватка полезная (🟢)
1. [Инструмент 1] — зачем нужен

## Что уже есть (✅)
1. [Инструмент] — где находится
```

---

### ФАЗА 3: СОЗДАНИЕ ИНСТРУМЕНТОВ (90 минут)

Создай **реальные работающие инструменты** в `/opt/leviathan-docs/tools/`.

#### Минимальный набор (обязательно):

**1. `lev-cli` — главный CLI для управления платформой**
```bash
/opt/leviathan-docs/tools/lev-cli
```
Функции:
- `lev-cli status` — статус всех проектов
- `lev-cli list` — список проектов с приоритетами
- `lev-cli audit` — запуск аудита
- `lev-cli health` — здоровье системы (RAM, Swap, Load)
- `lev-cli backup` — бэкап всех проектов
- `lev-cli docs` — открыть документацию

**2. `lev-status` — единый статус всех сервисов**
```bash
/opt/leviathan-docs/tools/lev-status
```
Выводит таблицу:
```
PROJECT              STATUS    UPTIME    CPU    RAM    PORT
leviathan_engine     🟢 UP     3d 5h     2%     150MB  8000
triton_agent         🟢 UP     1d 2h     1%     80MB   8001
pelleto-ai           🔴 DOWN   -         -      -      -
...
```

**3. `lev-health` — проверка здоровья сервера**
```bash
/opt/leviathan-docs/tools/lev-health
```
Проверяет:
- RAM/Swap/Load/Disk
- Открытые порты
- Запущенные сервисы
- Размер логов
- Устаревшие пакеты

**4. `lev-audit` — автоматический аудит**
```bash
/opt/leviathan-docs/tools/lev-audit
```
Запускает:
- Поиск секретов (`grep -r "ghp_\|sk_"`)
- Проверку TODO/FIXME
- Анализ размера проектов
- Git активность
- Сохраняет отчёт в `/root/analysis/audit/`

**5. `lev-backup` — бэкап всех проектов**
```bash
/opt/leviathan-docs/tools/lev-backup
```
Делает:
- `git bundle` для каждого git-репо
- Архив для non-git проектов
- Сохраняет в `/root/backups/YYYY-MM-DD/`

**6. Web Dashboard (опционально, если есть время)**
```bash
/opt/leviathan-docs/tools/dashboard/
```
Flask/FastAPI приложение с:
- Статус всех проектов
- Графики метрик
- Логи в реальном времени
- Запуск команд через UI

#### Дополнительные инструменты (если останется время):

- `lev-deploy` — деплой проектов
- `lev-rollback` — откат к предыдущей версии
- `lev-secrets` — сканер секретов
- `lev-perf` — анализ производительности
- `lev-docs-gen` — генерация документации из кода

#### Требования к инструментам:

1. **Shell scripts (bash)** — для простых утилит
2. **Python** — для сложных с логикой
3. **Исполняемые:** `chmod +x` на всех скриптах
4. **В PATH:** создать symlink в `/usr/local/bin/`
5. **Справка:** `--help` у каждой команды
6. **Цветной вывод:** использование ANSI цветов
7. **JSON output:** опция `--json` для автоматизации
8. **Безопасность:** не хардкодить секреты, использовать env/vault

#### Установка в PATH:
```bash
for tool in /opt/leviathan-docs/tools/lev-*; do
    ln -sf "$tool" /usr/local/bin/$(basename "$tool")
done
```

**Результат фазы 3:**
- 5+ работающих CLI-инструментов в `/opt/leviathan-docs/tools/`
- Все доступны через PATH (`lev-cli`, `lev-status`, etc.)
- Каждый имеет `--help`
- README в `/opt/leviathan-docs/tools/README.md`

---

### ФАЗА 4: ДЕПЛОЙ В GITHUB + PAGES (30 минут)

#### 4.1 Коммит в main
```bash
cd /opt/leviathan-docs

# Добавляем инструменты
git add tools/
git add docs/tools/  # если есть документация по инструментам

# Коммитим
git config user.email "agent@leviathan.local"
git config user.name "Leviathan Agent"
git commit -m "feat: Add platform tools (lev-cli, lev-status, lev-audit, lev-health, lev-backup)

- lev-cli: main CLI for platform management
- lev-status: unified status of all services
- lev-health: server health check
- lev-audit: automated security/code audit
- lev-backup: backup all projects

All tools available in PATH via /usr/local/bin/ symlinks"

git push origin main
```

#### 4.2 Обновление документации

Создай `/opt/leviathan-docs/docs/tools/INDEX.md`:
```markdown
# 🛠️ Инструменты платформы

## CLI-утилиты

- [lev-cli](lev-cli.md) — главный CLI
- [lev-status](lev-status.md) — статус всех сервисов
- [lev-health](lev-health.md) — здоровье сервера
- [lev-audit](lev-audit.md) — автоматический аудит
- [lev-backup](lev-backup.md) — бэкап проектов

## Установка

Все инструменты установлены в `/opt/leviathan-docs/tools/` и доступны через PATH:

```bash
lev-cli --help
lev-status
lev-health
```
```

Для каждого инструмента создай `docs/tools/TOOL_NAME.md` с:
- Описание
- Использование
- Примеры
- Опции

Обнови `mkdocs.yml`:
```yaml
nav:
  - Главная: index.md
  - Инструменты:
    - Обзор: tools/INDEX.md
    - lev-cli: tools/lev-cli.md
    - lev-status: tools/lev-status.md
    - lev-health: tools/lev-health.md
    - lev-audit: tools/lev-audit.md
    - lev-backup: tools/lev-backup.md
  - Быстрый старт:
    - Мастер-промт: prompts/MASTER_PROMPT.md
    - Чек-лист: plans/MASTER_CHECKLIST.md
  - Документация:
    - Манифест 5.0.0: context/MANIFEST_5.0.0.md
    - ADR-002 Final: context/ADR-002/ADR-002-FINAL.md
  - Логи: IMPLEMENTATION_LOG.md
  - Для AI: AI_README.md
```

#### 4.3 Сборка MkDocs
```bash
source /opt/mkdocs-venv/bin/activate
mkdocs build
```

#### 4.4 Деплой на gh-pages
```bash
# Копируем site во временную папку
rm -rf /tmp/leviathan-site
cp -r site /tmp/leviathan-site

# Переключаемся на gh-pages
git checkout gh-pages

# Чистим (сохраняя .git)
find . -maxdepth 1 -not -name '.' -not -name '.git' -exec rm -rf {} +

# Копируем новый сайт
cp -r /tmp/leviathan-site/* .
touch .nojekyll

# Коммитим и пушим
git add .
git commit -m "docs: Add tools documentation"
git push origin gh-pages

# Возвращаемся на main
git checkout main
```

**Результат фазы 4:**
- ✅ Инструменты в GitHub main ветке
- ✅ Документация по инструментам на сайте
- ✅ Сайт обновлён: https://lidenal85-blip.github.io/leviathan-docs/tools/

---

## 📊 ОЖИДАЕМЫЕ РЕЗУЛЬТАТЫ

### Файлы созданы:
```
/root/analysis/
├── ARCHITECTURAL_AUDIT_2026-07-03.md
├── audit/
│   ├── PROJECT_LIST.md
│   ├── TOOLING_GAP_ANALYSIS.md
│   ├── project1_audit.md
│   └── ... (60+ файлов)

/opt/leviathan-docs/
├── tools/
│   ├── lev-cli              # Главный CLI
│   ├── lev-status           # Статус сервисов
│   ├── lev-health           # Здоровье сервера
│   ├── lev-audit            # Аудит
│   ├── lev-backup           # Бэкап
│   └── README.md            # Документация
├── docs/
│   ├── tools/
│   │   ├── INDEX.md
│   │   ├── lev-cli.md
│   │   ├── lev-status.md
│   │   └── ...
│   └── ...
└── mkdocs.yml               # Обновлённый
```

### Команды доступны:
```bash
lev-cli --help
lev-status
lev-health
lev-audit
lev-backup
```

### Сайт обновлён:
- 🏠 https://lidenal85-blip.github.io/leviathan-docs/
- 🛠️ https://lidenal85-blip.github.io/leviathan-docs/tools/
- 📋 https://lidenal85-blip.github.io/leviathan-docs/tools/INDEX/

---

## 🎯 КРИТЕРИИ ЗАВЕРШЕНИЯ

Работа считается завершённой когда:

1. [ ] **Аудит проведён** — 60+ проектов найдено и проанализировано
2. [ ] **Gap-анализ сделан** — `TOOLING_GAP_ANALYSIS.md` создан
3. [ ] **Минимум 5 инструментов создано** — все в `/opt/leviathan-docs/tools/`
4. [ ] **Все инструменты работают** — `lev-cli`, `lev-status`, etc. отвечают на `--help`
5. [ ] **Инструменты в PATH** — доступны из любой директории
6. [ ] **Документация создана** — `docs/tools/` с описанием каждого
7. [ ] **MkDocs собирается** — без ошибок
8. [ ] **Закоммичено в main** — инструменты + документация
9. [ ] **Задеплоено на gh-pages** — сайт обновлён
10. [ ] **Сайт работает** — https://lidenal85-blip.github.io/leviathan-docs/tools/ открывается

---

## 🚀 ПЛАН ПО ВРЕМЕНИ

| Фаза | Время | Действие |
|------|-------|----------|
| **1. Аудит** | 60 мин | Найти 60+ проектов, проанализировать |
| **2. Gap-анализ** | 30 мин | Определить нехватку инструментов |
| **3. Создание** | 90 мин | Написать 5+ инструментов |
| **4. Деплой** | 30 мин | GitHub + MkDocs + Pages |
| **Буфер** | 30 мин | На непредвиденное |
| **ИТОГО** | **4 часа** | |

---

## 🎯 ПРИНЦИПЫ РАБОТЫ

1. **Начни с аудита** — без понимания платформы нельзя создать правильные инструменты
2. **Инструменты должны РЕШАТЬ реальные проблемы** — не создавай "для галочки"
3. **Каждый инструмент должен работать** — протестируй перед коммитом
4. **Документация обязательна** — без неё инструменты бесполезны
5. **Деплой в конце** — только когда всё готово и протестировано
6. **Не хардкодь секреты** — используй env переменные или vault
7. **Делай инструменты user-friendly** — цветной вывод, `--help`, понятные ошибки

---

## 🚀 НАЧИНАЙ СЕЙЧАС

У тебя **4 часа**. План ясен.

**Финальная проверка через 4 часа:**
```bash
lev-cli status
lev-health
open https://lidenal85-blip.github.io/leviathan-docs/tools/
```

**Удачи! 🔥**
```

---

## 📌 КАК ИСПОЛЬЗОВАТЬ

1. **Скопируй промт целиком**
2. **Отправь Claude Code (Fabi)**
3. **Через 4 часа получишь:**
   - Полный аудит 60+ проектов
   - 5+ работающих CLI-инструментов (`lev-cli`, `lev-status`, etc.)
   - Документацию по инструментам на сайте
   - Всё задеплоено в GitHub + Pages

---

## 🎯 ЧТО ПОЛУЧИМ

| Результат | Где |
|-----------|-----|
| Аудит всех проектов | `/root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md` |
| Список проектов | `/root/analysis/audit/PROJECT_LIST.md` |
| Gap-анализ | `/root/analysis/audit/TOOLING_GAP_ANALYSIS.md` |
| CLI-инструменты | `/opt/leviathan-docs/tools/` |
| В PATH | `/usr/local/bin/lev-*` |
| Документация | https://lidenal85-blip.github.io/leviathan-docs/tools/ |
| GitHub | https://github.com/lidenal85-blip/leviathan-docs |

**Это будет полноценный инструментарий для управления платформой!** 🚀
