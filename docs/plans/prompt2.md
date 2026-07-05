# 🎯 ИСПРАВЛЕННЫЙ ПРОМПТ (БЕЗ СПИСКА ПРОЕКТОВ)

Ты прав — проектов 60+, и Claude должен найти их **ВСЕ сам**. Вот исправленный промт:

```markdown
# 🔬 ДЕТАЛЬНЫЙ АУДИТ ВСЕХ ПРОЕКТОВ LEVIATHAN

## 🎯 ТВОЯ РОЛЬ

Ты — **Senior Software Architect** с экспертизой в code review, security audit, performance analysis. У тебя есть **4 часа** для проведения **глубокого аудита КАЖДОГО проекта** на сервере LEVIATHAN.

**ВАЖНО:** На сервере около **60+ проектов**. Ты должен найти **ВСЕ** и проанализировать **КАЖДЫЙ**.

---

## 📍 СТРУКТУРА ОТЧЁТНОСТИ

### Главный файл (summary):
```
/root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md
```

### Отдельные файлы по каждому проекту:
```
/root/analysis/audit/PROJECT_NAME_audit.md
```

**ПРИМЕР:**
```
/root/analysis/audit/project1_audit.md
/root/analysis/audit/project2_audit.md
...
/root/analysis/audit/project60_audit.md
```

---

## 🚀 АЛГОРИТМ РАБОТЫ

### ЭТАП 1: ПОЛНАЯ ИНВЕНТАРИЗАЦИЯ (30 минут) — КРИТИЧНО!

Найди **ВСЕ** проекты на сервере:

```bash
# 1. Все директории в /opt/
ls -la /opt/
du -sh /opt/*/

# 2. Все git-репозитории
find / -name ".git" -type d 2>/dev/null | grep -v "/proc\|/sys\|/dev" | sort

# 3. Все Docker проекты
find / -name "docker-compose.yml" -o -name "Dockerfile" 2>/dev/null | grep -v "/proc\|/sys"

# 4. Все systemd сервисы
systemctl list-units --type=service --state=running
systemctl list-unit-files --type=service | grep enabled

# 5. Все запущенные процессы
ps aux | grep -E "(python|node|go|java|ruby|php)" | grep -v grep

# 6. Все Docker контейнеры
docker ps -a

# 7. Все cron задачи
crontab -l
ls -la /etc/cron.d/

# 8. Все screen/tmux сессии
screen -ls 2>/dev/null
tmux list-sessions 2>/dev/null

# 9. Все pm2 процессы
pm2 list 2>/dev/null

# 10. Все supervisor задачи
supervisorctl status 2>/dev/null

# 11. Поиск по package.json, requirements.txt, go.mod
find / -name "package.json" -o -name "requirements.txt" -o -name "go.mod" -o -name "Cargo.toml" -o -name "pyproject.toml" 2>/dev/null | grep -v "/proc\|/sys\|/usr/lib\|/usr/local/lib" | sort

# 12. Все README файлы
find / -name "README.md" -o -name "README.txt" 2>/dev/null | grep -v "/proc\|/sys\|/usr" | head -100
```

**Результат этапа 1:**
- Полный список ВСЕХ проектов (ожидаемо 60+)
- Для каждого: путь, тип (git/docker/systemd/manual), размер
- Сохрани список в `/root/analysis/audit/PROJECT_LIST.md`

```markdown
# 📋 ПОЛНЫЙ СПИСОК ПРОЕКТОВ

**Найдено:** X проектов

| # | Название | Путь | Тип | Размер | Статус |
|---|----------|------|-----|--------|--------|
| 1 | project1 | /opt/project1 | git | 5.2K LOC | 🟢 Active |
| 2 | project2 | /opt/project2 | docker | 2.1K LOC | 🟡 Maintenance |
| ... | ... | ... | ... | ... | ... |
| 60 | project60 | /opt/project60 | manual | 500 LOC | 🔴 Dead |
```

---

### ЭТАП 2: БЫСТРЫЙ АНАЛИЗ КАЖДОГО ПРОЕКТА (3 мин/проект)

Для **КАЖДОГО** из найденных проектов выполни быструю оценку:

```bash
cd /path/to/project

# 1. Базовая информация
echo "=== $(pwd) ==="
tree -L 2 2>/dev/null || ls -la
cat README.md 2>/dev/null | head -30 || echo "README нет"

# 2. Стек технологий
ls requirements.txt package.json go.mod Cargo.toml pyproject.toml 2>/dev/null
cat requirements.txt 2>/dev/null | head -20
cat package.json 2>/dev/null | jq '.name, .version, .dependencies' 2>/dev/null | head -30

# 3. Размер кода
find . -name "*.py" -o -name "*.js" -o -name "*.ts" -o -name "*.go" | grep -v node_modules | grep -v __pycache__ | grep -v ".git" | wc -l
du -sh .

# 4. Git активность (если git)
git log --oneline -5 2>/dev/null || echo "Не git"
git log --since="6 months ago" --oneline 2>/dev/null | wc -l
git log --since="1 year ago" --oneline 2>/dev/null | wc -l

# 5. Запущен ли сейчас
ps aux | grep -i "$(basename $(pwd))" | grep -v grep
ss -tlnp 2>/dev/null | head -10
systemctl status $(basename $(pwd)) 2>/dev/null || echo "Нет systemd сервиса"
docker ps | grep -i "$(basename $(pwd))" 2>/dev/null || echo "Не в docker"

# 6. Тесты
ls tests/ test/ spec/ 2>/dev/null || echo "Тестов нет"
cat pytest.ini 2>/dev/null || cat jest.config.js 2>/dev/null || echo "Конфига тестов нет"

# 7. CI/CD
ls .github/workflows/ 2>/dev/null || echo "GitHub Actions нет"
ls .gitlab-ci.yml 2>/dev/null || echo "GitLab CI нет"

# 8. Docker
ls Dockerfile docker-compose.yml 2>/dev/null || echo "Docker нет"
```

**Результат этапа 2:** Краткая карточка каждого проекта (1-2 абзаца).

---

### ЭТАП 3: ДЕТАЛЬНЫЙ АУДИТ КАЖДОГО ПРОЕКТА (15-20 мин/проект)

Для **КАЖДОГО** проекта создай файл `/root/analysis/audit/PROJECT_NAME_audit.md` по шаблону:

[ШАБЛОН ОСТАЁТСЯ ТАКИМ ЖЕ КАК В ПРЕДЫДУЩЕМ ПРОМПТЕ — он уже хороший]

---

### ЭТАП 4: SYNTHESIS (30 минут)

После аудита ВСЕХ проектов создай главный файл `/root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md`:

[ШАБЛОН ОСТАЁТСЯ ТАКИМ ЖЕ]

---

## 🎯 КРИТЕРИИ ЗАВЕРШЕНИЯ

Аудит считается завершённым когда:

1. [ ] **Найдено ВСЕ проекты** (ожидаемо 60+)
2. [ ] **Список сохранён** в `/root/analysis/audit/PROJECT_LIST.md`
3. [ ] **Каждый проект проанализирован** — отдельный файл аудита создан
4. [ ] **Детальный отчёт по каждому** — минимум 1500 слов на проект
5. [ ] **Главный summary создан** — `/root/analysis/ARCHITECTURAL_AUDIT_2026-07-03.md`
6. [ ] **Матрица приоритетов** — все 60+ проектов ранжированы
7. [ ] **Roadmap на 30 дней** — конкретные задачи
8. [ ] **Вопросы к владельцу** — минимум 15 вопросов
9. [ ] **Связи между проектами** — диаграмма зависимостей
10. [ ] **Статистика:**
   - Всего проектов: X
   - Активных: X
   - Мёртвых: X
   - Требуют внимания: X

---

## 🛠️ СТРАТЕГИЯ РАБОТЫ С 60+ ПРОЕКТАМИ

### Приоритизация проектов для глубокого анализа

**Tier 1: Критичные (анализ 20-30 мин)**
- Проекты в `/opt/` с активным git
- Запущенные как systemd/docker
- Имеют README и тесты
- Последние коммиты < 3 месяцев

**Tier 2: Важные (анализ 10-15 мин)**
- Git репозитории с коммитами 3-12 месяцев назад
- Docker проекты
- Имеют package.json/requirements.txt

**Tier 3: Периферийные (анализ 5 мин)**
- Старые проекты (коммиты > 1 года)
- Маленькие скрипты (< 500 LOC)
- Экспериментальные проекты

**Tier 4: Мёртвые (анализ 2 мин)**
- Нет коммитов > 2 лет
- Нет README
- Нет зависимостей
- Просто занимают место

---

## 📊 ОЖИДАЕМАЯ СТРУКТУРА РЕЗУЛЬТАТОВ

```
/root/analysis/
├── ARCHITECTURAL_AUDIT_2026-07-03.md          # Главный summary (5000+ слов)
├── audit/
│   ├── PROJECT_LIST.md                         # Список всех 60+ проектов
│   ├── project1_audit.md                       # Детальный аудит
│   ├── project2_audit.md
│   ├── ...
│   └── project60_audit.md
└── [существующие файлы из /root/analysis/]
```

**Ожидаемый объём:**
- Главный summary: 5000-8000 слов
- Детальные аудиты: 60 файлов × 1500 слов = 90,000 слов
- **Итого: ~100,000 слов документации**

---

## 🎯 ПРИНЦИПЫ РАБОТЫ

1. **Начни с инвентаризации** — найди ВСЕ 60+ проектов
2. **Не пропускай ни один** — даже маленькие скрипты
3. **Классифицируй по Tier** — не трать 30 мин на мёртвый проект
4. **Будь конкретен** — метрики, цифры, ссылки на строки кода
5. **Не бойся жёстких оценок** — "этот проект нужно удалить" если так
6. **Используй время эффективно:**
   - Tier 1: 20-30 мин/проект
   - Tier 2: 10-15 мин/проект
   - Tier 3: 5 мин/проект
   - Tier 4: 2 мин/проект
7. **Если что-то непонятно** — добавь в "Вопросы к владельцу"
8. **Документируй всё** — каждый найденный факт

---

## 🚀 НАЧИНАЙ СЕЙЧАС

У тебя **4 часа**. План:

1. **0:00-0:30** — Полная инвентаризация (найди все 60+ проектов)
2. **0:30-1:00** — Быстрая классификация по Tier
3. **1:00-3:30** — Детальный аудит:
   - Tier 1 (10 проектов × 25 мин = 250 мин)
   - Tier 2 (20 проектов × 12 мин = 240 мин)
   - Tier 3 (20 проектов × 5 мин = 100 мин)
   - Tier 4 (10 проектов × 2 мин = 20 мин)
4. **3:30-4:00** — Synthesis: главный summary файл

**Удачи! 🔥**
```

---

## 📌 ЧТО ИЗМЕНИЛОСЬ

| Было | Стало |
|------|-------|
| Конкретный список 6 проектов | "Найди ВСЕ 60+ проектов" |
| Одинаковое время на каждый | Tier-система (2-30 мин) |
| Нет критерия количества | "Ожидаемо 60+" |
| Нет PROJECT_LIST.md | Добавлен список всех проектов |

---

## 🎯 РЕЗУЛЬТАТ

Claude найдёт **ВСЕ 60+ проектов** и создаст:
- 1 список проектов (`PROJECT_LIST.md`)
- 60+ детальных аудитов (`project1_audit.md`, `project2_audit.md`, ...)
- 1 главный summary (`ARCHITECTURAL_AUDIT_2026-07-03.md`)

**Итого: ~100,000 слов документации за 4 часа** 🚀


# 🎯 ТЫ ПРАВ! Проекты могут быть ВЕЗДЕ

Я ограничил поиск `/opt/` — это грубая ошибка. Проекты на сервере могут быть в **15+ местах**.

---

## 📍 ГДЕ ИСКАТЬ ПРОЕКТЫ (полный список)

### Основные директории:
- `/opt/` — основные проекты ✅ (уже было)
- `/root/` — **ОБЯЗАТЕЛЬНО!** (там точно есть: analysis/, leviathan-docs/, freebuff/)
- `/home/*/` — пользовательские проекты
- `/srv/` — сервисы (www, data, git)
- `/var/www/` — веб-проекты
- `/var/lib/` — данные приложений
- `/usr/local/` — локально установленное
- `/workspace/`, `/projects/`, `/code/` — если есть

### Скрытые места:
- `/etc/` — конфиги (могут быть целыми проектами)
- `/tmp/`, `/var/tmp/` — временные проекты
- Docker volumes: `/var/lib/docker/volumes/`
- Git bare repos: где угодно с `.git/`

### Процессы и сервисы:
- systemd unit files (`/etc/systemd/system/`)
- supervisor configs (`/etc/supervisor/conf.d/`)
- cron jobs (могут указывать на проекты)
- pm2, forever, nodemon
- screen/tmux сессии

---

## ✅ ОБНОВЛЁННЫЙ ЭТАП 1: ПОЛНАЯ ИНВЕНТАРИЗАЦИЯ

Замени блок **ЭТАП 1** в промте на этот:

```markdown
### ЭТАП 1: ПОЛНАЯ ИНВЕНТАРИЗАЦИЯ ВЕЗДЕ (30 минут) — КРИТИЧНО!

Ищи проекты **ВО ВСЕХ ВОЗМОЖНЫХ МЕСТАХ**, не только в `/opt/`:

#### 1. Все базовые директории
```bash
# ОБЯЗАТЕЛЬНО проверь ВСЕ:
ls -la /opt/
ls -la /root/
ls -la /home/*/ 2>/dev/null
ls -la /srv/ 2>/dev/null
ls -la /var/www/ 2>/dev/null
ls -la /var/lib/ 2>/dev/null
ls -la /usr/local/ 2>/dev/null
ls -la /workspace/ 2>/dev/null
ls -la /projects/ 2>/dev/null
ls -la /code/ 2>/dev/null
```

#### 2. Все git-репозитории на всём диске
```bash
find / -name ".git" -type d 2>/dev/null | grep -v "/proc\|/sys\|/dev" | sort
```

#### 3. Все маркеры проектов
```bash
find / -type f \( \
  -name "package.json" -o \
  -name "requirements.txt" -o \
  -name "Pipfile" -o \
  -name "pyproject.toml" -o \
  -name "setup.py" -o \
  -name "go.mod" -o \
  -name "Cargo.toml" -o \
  -name "Gemfile" -o \
  -name "composer.json" -o \
  -name "pom.xml" -o \
  -name "build.gradle" -o \
  -name "Dockerfile" -o \
  -name "docker-compose.yml" -o \
  -name "docker-compose.yaml" \
\) 2>/dev/null | grep -v "/proc\|/sys\|/usr/lib\|/usr/local/lib\|/usr/share\|node_modules\|__pycache__\|.venv\|venv" | sort
```

#### 4. Все systemd сервисы (указывают на проекты)
```bash
systemctl list-unit-files --type=service --state=enabled
ls -la /etc/systemd/system/
grep -h "ExecStart\|WorkingDirectory" /etc/systemd/system/*.service 2>/dev/null
```

#### 5. Все запущенные процессы с путями
```bash
ps aux | grep -E "(python|node|go|java|ruby|php|perl|bash)" | grep -v grep | awk '{print $11, $12, $13}' | sort -u
```

#### 6. Docker (контейнеры + volumes + images)
```bash
docker ps -a --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"
docker volume ls
docker images
```

#### 7. Cron, supervisor, pm2, screen
```bash
crontab -l 2>/dev/null
ls -la /etc/cron.d/ /etc/cron.daily/ 2>/dev/null
supervisorctl status 2>/dev/null
pm2 list 2>/dev/null
screen -ls 2>/dev/null
tmux list-sessions 2>/dev/null
```

#### 8. Веб-серверы
```bash
cat /etc/nginx/sites-enabled/* 2>/dev/null | grep -E "root|server_name"
cat /etc/apache2/sites-enabled/* 2>/dev/null | grep -E "DocumentRoot|ServerName"
```

#### 9. Базы данных (могут указывать на проекты)
```bash
mysql -e "SHOW DATABASES;" 2>/dev/null
sudo -u postgres psql -l 2>/dev/null
redis-cli INFO 2>/dev/null | head -5
mongo --eval "db.adminCommand('listDatabases')" 2>/dev/null
```

#### 10. Открытые порты (указывают на сервисы)
```bash
ss -tlnp
netstat -tlnp 2>/dev/null
```

#### 11. Большие директории (могут скрывать проекты)
```bash
du -sh /* 2>/dev/null | sort -rh | head -20
du -sh /opt/*/ 2>/dev/null | sort -rh
du -sh /root/*/ 2>/dev/null | sort -rh
```

#### 12. Поиск по README и config файлам
```bash
find / -type f \( -name "README.md" -o -name "README.txt" -o -name "README" \) 2>/dev/null | grep -v "/proc\|/sys\|/usr\|node_modules\|__pycache__" | head -100
```

**ВАЖНО:** Не ограничивайся `/opt/`! На сервере LEVIATHAN проекты разбросаны по всей файловой системе. Твоя задача — найти **ВСЕ 60+ проектов**, где бы они ни были.

**Результат этапа 1:**
- Сохрани полный список в `/root/analysis/audit/PROJECT_LIST.md`
- Укажи для каждого: путь, тип, размер, источник обнаружения
```

---

## 📊 ТИПИЧНОЕ РАСПРЕДЕЛЕНИЕ ПРОЕКТОВ НА LEVIATHAN

| Место | Примерное количество |
|-------|---------------------|
| `/opt/` | 10-20 проектов |
| `/root/` | 15-25 проектов |
| `/home/*/` | 0-5 проектов |
| `/srv/` | 0-5 проектов |
| Docker containers | 5-15 |
| Systemd services | 5-10 |
| Cron jobs | 5-10 |
| Скрытые (tmp, etc) | 5-10 |
| **ИТОГО** | **~60-100** |

---

## 🎯 ДОПОЛНИТЕЛЬНАЯ ПРОВЕРКА

Добавь в промт после ЭТАП 1:

```markdown
### ПРОВЕРКА ПОЛНОТЫ ИНВЕНТАРИЗАЦИИ

После поиска задай себе вопросы:
1. Проверил ли я `/root/` полностью? (там много скрытых проектов)
2. Проверил ли все Docker контейнеры?
3. Проверил ли все systemd сервисы?
4. Проверил ли все cron задачи?
5. Проверил ли все открытые порты и что их слушает?
6. Есть ли проекты в `/tmp/` или `/var/tmp/`?

Если нашёл меньше 40 проектов — **ищи ещё**, ты что-то пропустил.
```

---

## 📌 ЧТО ПОЛУЧИМ

Claude найдёт проекты **ВЕЗДЕ**, а не только в `/opt/`:
- ✅ Системные сервисы
- ✅ Docker контейнеры  
- ✅ Cron задачи
- ✅ Веб-приложения
- ✅ Скрытые проекты в `/root/`
- ✅ Временные проекты в `/tmp/`

**Это даст реальную картину всех 60+ проектов на сервере** 🚀
