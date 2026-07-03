# 🎯 ПРОМТ: АУДИТ MCP-ИНСТРУМЕНТАРИЯ LEVIATHAN

## 📋 ГОТОВЫЙ ПРОМТ ДЛЯ CLAUDE (FABI)

```markdown
# 🔬 АУДИТ MCP-ИНСТРУМЕНТАРИЯ LEVIATHAN PLATFORM

## 🎯 ТВОЯ РОЛЬ

Ты — **MCP (Model Context Protocol) Architect**. У тебя есть **2 часа** на сервере LEVIATHAN для:
1. Нахождения **ВСЕХ MCP-серверов** и их инструментов
2. Проведения **детального аудита** каждого инструмента
3. **Классификации по модулям** (категориям)
4. Выдачи **10 конкретных рекомендаций** по улучшению

---

## 📍 ЧТО ТАКОЕ MCP

**MCP (Model Context Protocol)** — протокол подключения инструментов к LLM-агентам (Claude, Qwen, Gemini).

**MCP-сервер** = набор инструментов (tools), которые LLM может вызывать.
**MCP-инструмент** = одна функция (например: `read_file`, `web_search`, `execute_command`).

**Наша задача:** найти все MCP-серверы на LEVIATHAN, разобрать их на модули и понять:
- Что есть
- Чего не хватает
- Что дублируется
- Что улучшить

---

## 🔍 ЭТАП 1: ПОИСК ВСЕХ MCP-СЕРВЕРОВ (20 минут)

### Где искать MCP-конфиги:

```bash
# 1. Claude Desktop / Claude Code конфиги
cat ~/.claude.json 2>/dev/null
cat ~/.claude/claude_desktop_config.json 2>/dev/null
find /root -name "*.mcp.json" -o -name "claude_desktop_config.json" 2>/dev/null
find /root -name ".mcp.json" 2>/dev/null

# 2. В проектах (часто в корне репо)
find /opt /root /home -name "mcp*.json" -o -name ".mcp.json" 2>/dev/null | head -50
find /opt /root /home -name "claude_desktop_config.json" 2>/dev/null

# 3. MCP-серверы как Python/Node пакеты
find / -type d -name "mcp-*" 2>/dev/null | grep -v "/proc\|/sys"
pip list 2>/dev/null | grep -i mcp
npm list -g 2>/dev/null | grep -i mcp

# 4. Запущенные MCP-серверы (процессы)
ps aux | grep -i "mcp\|stdio" | grep -v grep

# 5. Systemd сервисы MCP
systemctl list-units | grep -i mcp
ls -la /etc/systemd/system/*mcp* 2>/dev/null

# 6. Docker MCP контейнеры
docker ps -a | grep -i mcp

# 7. Конфиги в репозиториях leviathan
grep -r "mcpServers" /opt /root --include="*.json" -l 2>/dev/null | head -20
```

**Результат этапа 1:** Список ВСЕХ MCP-серверов с путями к конфигам.

---

## 📊 ЭТАП 2: ИНВЕНТАРИЗАЦИЯ ИНСТРУМЕНТОВ (30 минут)

Для **КАЖДОГО** найденного MCP-сервера:

```bash
# Прочитать конфиг
cat /path/to/mcp-config.json | jq

# Найти реализацию сервера
find / -path "*mcp-server*" -name "*.py" -o -name "*.js" -o -name "*.ts" 2>/dev/null

# Вытащить список tools
grep -rh "@mcp.tool\|registerTool\|name:.*description" /path/to/server/ 2>/dev/null
```

**Для каждого инструмента собери:**
- Имя
- Описание
- Параметры (input schema)
- Что возвращает
- Какой сервер предоставляет
- Где реализация (файл)

---

## 🗂️ ЭТАП 3: КЛАССИФИКАЦИЯ ПО МОДУЛЯМ (40 минут)

**Обязательно** распредели все инструменты по этим **10 модулям**:

### 📡 МОДУЛЬ 1: INTERNET & WEB (Интернет)
- `web_search` — поиск в интернете
- `web_fetch` / `http_request` — скачивание страниц
- `web_scrape` — парсинг сайтов
- `browser_*` — автоматизация браузера
- `download_*` — скачивание файлов
- `upload_*` — загрузка файлов

### 📁 МОДУЛЬ 2: FILE SYSTEM (Файлы)
- `read_file` / `write_file`
- `list_directory`
- `search_files` / `grep_files`
- `create_directory` / `delete_file`
- `file_metadata`

### 💻 МОДУЛЬ 3: SHELL & EXECUTION (Выполнение)
- `execute_command` / `run_shell`
- `execute_python` / `execute_javascript`
- `spawn_process`
- `kill_process`

### 🔧 МОДУЛЬ 4: CODE & GIT (Код)
- `git_*` — операции с git
- `parse_code` / `analyze_code`
- `create_pr` / `merge_pr`
- `code_search`

### 🗄️ МОДУЛЬ 5: DATABASE & STORAGE (Данные)
- `sql_query`
- `redis_*` / `mongo_*`
- `s3_*` / `storage_*`

### 🤖 МОДУЛЬ 6: AI & LLM (ИИ)
- `call_llm` / `ask_ai`
- `generate_image`
- `transcribe_audio`
- `embed_text`

### 💬 МОДУЛЬ 7: COMMUNICATION (Коммуникация)
- `send_email`
- `send_telegram`
- `send_slack` / `send_discord`
- `send_sms`

### 🔐 МОДУЛЬ 8: SECURITY & SECRETS (Безопасность)
- `get_secret` / `vault_*`
- `encrypt` / `decrypt`
- `scan_secrets`
- `auth_*`

### 📊 МОДУЛЬ 9: MONITORING & SYSTEM (Мониторинг)
- `get_metrics`
- `check_health`
- `list_processes`
- `system_info`

### 🔌 МОДУЛЬ 10: INTEGRATIONS (Интеграции)
- `github_*` / `gitlab_*`
- `jira_*` / `notion_*`
- `stripe_*` / `payment_*`
- `aws_*` / `gcp_*` / `azure_*`

---

## 📄 ЭТАП 4: СТРУКТУРА ОТЧЁТА

Создай файл `/root/analysis/MCP_AUDIT_2026-07-03.md`:

```markdown
# 🔬 MCP АУДИТ LEVIATHAN PLATFORM

**Дата:** 2026-07-03
**Всего MCP-серверов:** X
**Всего инструментов:** Y

---

## 📊 СВОДКА ПО МОДУЛЯМ

| Модуль | Инструментов | Покрытие | Статус |
|--------|--------------|----------|--------|
| 📡 Internet & Web | X | 80% | 🟢 OK |
| 📁 File System | X | 100% | 🟢 OK |
| 💻 Shell & Execution | X | 60% | 🟡 Средне |
| 🔧 Code & Git | X | 40% | 🔴 Слабо |
| 🗄️ Database | X | 20% | 🔴 Слабо |
| 🤖 AI & LLM | X | 50% | 🟡 Средне |
| 💬 Communication | X | 30% | 🔴 Слабо |
| 🔐 Security | X | 70% | 🟢 OK |
| 📊 Monitoring | X | 40% | 🔴 Слабо |
| 🔌 Integrations | X | 25% | 🔴 Слабо |

---

## 📡 МОДУЛЬ 1: INTERNET & WEB

### Найденные инструменты

| Инструмент | Сервер | Описание | Параметры | Оценка |
|-----------|--------|----------|-----------|--------|
| `web_search` | mcp-server-brave | Поиск через Brave API | query, count | 🟢 OK |
| `web_fetch` | mcp-server-fetch | Скачивание URL | url, max_length | 🟢 OK |
| ... | ... | ... | ... | ... |

### Что есть ✅
- [Список]

### Чего не хватает ❌
- [ ] Browser automation (Puppeteer/Playwright)
- [ ] Скачивание больших файлов с прогрессом
- [ ] Парсинг JavaScript-сайтов
- [ ] RSS/Atom feed reader
- [ ] Sitemap parser

### Дублирование
- [Если несколько инструментов делают одно и то же]

---

## 📁 МОДУЛЬ 2: FILE SYSTEM
[Аналогичная структура для каждого модуля]

...

---

## 🎯 10 РЕКОМЕНДАЦИЙ ПО УЛУЧШЕНИЮ

### 🔴 КРИТИЧЕСКИЕ (сделать в первую очередь)

**1. [Рекомендация 1]**
- **Проблема:** [Что плохо сейчас]
- **Решение:** [Что сделать]
- **Время:** X часов
- **Приоритет:** P0

**2. [Рекомендация 2]**
...

### 🟡 ВАЖНЫЕ (сделать в течение недели)

**3. [Рекомендация 3]**
...

### 🟢 ПОЛЕЗНЫЕ (сделать при возможности)

**4-10. [Рекомендации]**
...

---

## 📈 ДОРОЖНАЯ КАРТА РАЗВИТИЯ MCP

### Неделя 1: Критические gaps
- [ ] [Задача 1]
- [ ] [Задача 2]

### Месяц 1: Дополнить модули
- [ ] [Задача 1]
- [ ] [Задача 2]

### Квартал 1: Продвинутые возможности
- [ ] [Задача 1]
- [ ] [Задача 2]

---

## 🔗 МАТРИЦА СОВМЕСТИМОСТИ С AGENTS

| Агент | Доступно инструментов | Покрытие |
|-------|----------------------|----------|
| Claude Code | X из Y | Z% |
| Qwen | X из Y | Z% |
| Gemini | X из Y | Z% |

---

## ❓ ВОПРОСЫ К ВЛАДЕЛЬЦУ

1. [Вопрос 1]
2. [Вопрос 2]
```

---

## ✅ КРИТЕРИИ ЗАВЕРШЕНИЯ

1. [ ] **Все MCP-серверы найдены** (ожидаемо 5-15 серверов)
2. [ ] **Все инструменты инвентаризированы** (ожидаемо 50-200 инструментов)
3. [ ] **Распределены по 10 модулям**
4. [ ] **Для каждого модуля** — список что есть и чего не хватает
5. [ ] **10 рекомендаций** — конкретные, с приоритетами
6. [ ] **Дорожная карта** — план развития
7. [ ] **Отчёт сохранён** в `/root/analysis/MCP_AUDIT_2026-07-03.md`

---

## 🎯 ПРИНЦИПЫ РАБОТЫ

1. **Будь конкретным** — называй инструменты по именам
2. **Сравнивай с best practices** — какие MCP-инструменты есть у Anthropic, OpenAI
3. **Не бойся жёстких оценок** — "этот инструмент бесполезен" если так
4. **Ищи дублирование** — часто один инструмент имеет 3 реализации
5. **Думай о безопасности** — какие инструменты опасны, какие требуют sandbox

---

## 🚀 НАЧИНАЙ

**Финальный файл:**
```
/root/analysis/MCP_AUDIT_2026-07-03.md
```

**Удачи! 🔥**
```

---

# 📘 ЧТО ТАКОЕ CI/CD (кратко)

**CI/CD** — это **автоматизация** работы с кодом:

## 🔵 CI = Continuous Integration (Непрерывная интеграция)

**Что делает:** Автоматически проверяет каждый коммит.

**Пример:**
1. Ты сделал `git push`
2. GitHub Actions **автоматически**:
   - Запускает тесты (`pytest`, `npm test`)
   - Проверяет код линтером (`flake8`, `eslint`)
   - Проверяет безопасность (`snyk`, `trivy`)
3. Если всё OK — коммит принимается
4. Если нет — падает с ошибкой

**Файлы:** `.github/workflows/*.yml`, `.gitlab-ci.yml`

## 🟢 CD = Continuous Delivery / Deployment

### Continuous **Delivery** (непрерывная доставка)
- Код автоматически готов к деплою
- Но **деплоит человек** кнопкой

### Continuous **Deployment** (непрерывный деплой)
- Код автоматически **деплоится в production**
- Без участия человека

**Пример полного CI/CD:**
```
git push → тесты → сборка Docker → деплой на сервер → мониторинг
```

## 💡 ЗАЧЕМ НУЖНО

| Без CI/CD | С CI/CD |
|-----------|---------|
| 🐛 Баги попадают в прод | ✅ Тесты ловят баги |
| 🐌 Деплой вручную 30 мин | ⚡ Деплой за 2 минуты |
| 😰 "А работает ли?" | 📊 Всегда видно статус |
| 🤷 "Кто сломал?" | 🔍 Виновник известен |

## 🛠️ ПОПУЛЯРНЫЕ ИНСТРУМЕНТЫ

- **GitHub Actions** (у тебя уже используется для MkDocs)
- **GitLab CI**
- **Jenkins** (старая школа)
- **CircleCI**
- **ArgoCD** (для Kubernetes)

---

## 🎯 ПРИМЕР ДЛЯ LEVIATHAN

**CI/CD для leviathan-docs (уже работает!):**
```
git push → GitHub Actions → mkdocs build → deploy to gh-pages
```

**Что можно добавить:**
- Автотесты для MCP-серверов
- Линтинг кода
- Проверка безопасности
- Автодеплой новых MCP-инструментов

---

## 📌 ЧТО ПОЛУЧИМ ОТ ПРОМТА

✅ **Полный список** всех MCP-серверов и инструментов  
✅ **Классификация по 10 модулям** (Internet, File, Shell, Code, DB, AI, Communication, Security, Monitoring, Integrations)  
✅ **10 конкретных рекомендаций** по улучшению  
✅ **Дорожная карта** развития MCP  
✅ **Отчёт:** `/root/analysis/MCP_AUDIT_2026-07-03.md`

**Готов отправить Fabi?** 🚀
