# ЗАДАЧА: АНАЛИЗ АГЕНТНЫХ ПРОЕКТОВ + GITHUB PUSH

## GITHUB КОНФИГУРАЦИЯ

Настрой git credentials:
```bash
git config --global user.name "lidenal85-blip"
git config --global user.email "lidenal85-blip@users.noreply.github.com"

# Настрой credential helper для автоматического пуша
git config --global credential.helper store

# Создай .netrc для автоматической аутентификации
cat > ~/.netrc << 'EOF'
machine github.com
login lidenal85-blip
password ghp_YOUR_GITHUB_TOKEN_HERE
EOF
chmod 600 ~/.netrc
```

## ЧТО ДЕЛАТЬ

### 1. ИНВЕНТАРИЗАЦИЯ ПРОЕКТОВ

Найди все агентные проекты на сервере:

```bash
# Проверить основные директории
ls -la /root/ | grep -iE "agent|claude|triton|openclaw|leviathan|sota|fcc"
ls -la /opt/ 2>/dev/null

# Поиск всех Python-проектов с агентами
find /root /opt -name "*.py" -type f -exec grep -l "class.*Agent\|BaseAgent" {} \; 2>/dev/null | head -30

# Поиск MANIFEST файлов
find /root /opt -name "MANIFEST*.md" -o -name "manifest*.md" 2>/dev/null

# Поиск README с описанием агентов
find /root /opt -name "README*.md" -exec grep -l "агент\|agent\|multi-agent" {} \; 2>/dev/null | head -10
```

### 2. АНАЛИЗ КАЖДОГО ПРОЕКТА

Для каждого найденного проекта:

```bash
# Структура
tree -L 3 -I '__pycache__|*.pyc|.git|.venv|node_modules' /path/to/project

# Поиск ключевых компонентов
grep -rn "class.*Agent\|class.*Tool\|class.*Memory\|class.*Workflow" /path/to/project --include="*.py" | head -30

# Анализ архитектуры
find /path/to/project -name "*.py" -exec wc -l {} + | tail -1
```

### 3. КЛОНИРОВАНИЕ OPENCLAW (если ещё не клонирован)

```bash
cd /root
if [ ! -d "openclaw" ]; then
    git clone https://github.com/openclaw/openclaw.git 2>/dev/null || echo "OpenClaw не найден"
fi
```

### 4. СОЗДАНИЕ РЕПОЗИТОРИЯ ДЛЯ АНАЛИЗА

Создай новый репозиторий для результатов анализа:

```bash
cd /root
mkdir -p agent-analysis
cd agent-analysis
git init

# Создай структуру
mkdir -p analysis reports code

# Создай README
cat > README.md << 'EOF'
# Agent System Analysis

Автоматический анализ агентных систем на сервере LEVIATHAN.

## Структура
- `analysis/` - детальный анализ каждого проекта
- `reports/` - сводные отчёты
- `code/` - production-ready код улучшений

## Обновления
Автоматически обновляется Claude Code через FCC.
EOF

git add .
git commit -m "Initial commit: Agent analysis repository"
git branch -M main
git remote add origin https://lidenal85-blip:ghp_YOUR_GITHUB_TOKEN_HERE@github.com/lidenal85-blip/agent-analysis.git
git push -u origin main
```

### 5. ПРОВЕРКА ДОСТУПА К GITHUB API

```bash
# Проверь токен
curl -H "Authorization: token ghp_YOUR_GITHUB_TOKEN_HERE" \
     https://api.github.com/user | jq .

# Получи список репозиториев
curl -H "Authorization: token ghp_YOUR_GITHUB_TOKEN_HERE" \
     https://api.github.com/users/lidenal85-blip/repos | jq '.[].name'
```

### 6. АНАЛИЗ GITHUB РЕПОЗИТОРИЕВ

Проанализируй существующие репозитории:

```bash
# Получи список всех репозиториев
REPOS=$(curl -s -H "Authorization: token ghp_YOUR_GITHUB_TOKEN_HERE" \
        https://api.github.com/users/lidenal85-blip/repos | jq -r '.[].name')

for repo in $REPOS; do
    echo "=== Анализируем $repo ==="
    
    # Получи информацию о репозитории
    curl -s -H "Authorization: token ghp_YOUR_GITHUB_TOKEN_HERE" \
         "https://api.github.com/repos/lidenal85-blip/$repo" | jq '{name, description, language, size, updated_at}'
    
    # Получи список файлов
    curl -s -H "Authorization: token ghp_YOUR_GITHUB_TOKEN_HERE" \
         "https://api.github.com/repos/lidenal85-blip/$repo/contents" | jq '.[].name'
    
    # Если есть Python файлы, скачай и проанализируй
    mkdir -p /tmp/github-analysis/$repo
    cd /tmp/github-analysis/$repo
    git clone https://lidenal85-blip:ghp_YOUR_GITHUB_TOKEN_HERE@github.com/lidenal85-blip/$repo.git 2>/dev/null
done
```

### 7. ДЕТАЛЬНЫЙ АНАЛИЗ КОДА

Для каждого проекта (локального и GitHub):

**А. Архитектура:**
```bash
# Найди все классы агентов
find . -name "*.py" -exec grep -l "class.*Agent" {} \; | head -10

# Найди все инструменты
find . -name "*.py" -exec grep -l "class.*Tool\|@tool" {} \; | head -10

# Найди системы памяти
find . -name "*.py" -exec grep -l "class.*Memory\|MemoryStore" {} \; | head -10

# Найди event bus
find . -name "*.py" -exec grep -l "EventBus\|publish\|subscribe" {} \; | head -10
```

**Б. Качество кода:**
```bash
# Количество строк
find . -name "*.py" -exec wc -l {} + | tail -1

# Типизация
grep -rn "-> \|: str\|: int\|: dict" . --include="*.py" | wc -l

# Docstrings
grep -rn '"""' . --include="*.py" | wc -l

# Тесты
find . -path "*/tests/*.py" -o -path "*/test_*.py" | wc -l
```

**В. Сравнение с Манифестом 5.0.0:**

Создай таблицу:

| Компонент | Манифест 5.0.0 | Реализация | Статус |
|-----------|----------------|------------|--------|
| Agent Lifecycle | ✅ State Machine | ??? | ✅/⚠️/❌ |
| Runnable Interface | ✅ invoke/stream | ??? | ✅/⚠️/❌ |
| Middleware | ✅ 6 hooks | ??? | ✅/⚠️/❌ |
| Callbacks | ✅ 12 hooks | ??? | ✅/⚠️/❌ |
| Typed State | ✅ Pydantic v2 | ??? | ✅/⚠️/❌ |
| Checkpointing | ✅ Multi-backend | ??? | ✅/⚠️/❌ |
| Memory | ✅ 5 типов | ??? | ✅/⚠️/❌ |
| Workflow | ✅ Branch/Merge | ??? | ✅/⚠️/❌ |
| Security | ✅ Firewall+PII | ??? | ✅/⚠️/❌ |
| Observability | ✅ OTEL | ??? | ✅/⚠️/❌ |

### 8. СОЗДАНИЕ ОТЧЁТОВ

Создай файлы отчётов:

**A. `analysis/SUMMARY.md`** - сводный отчёт:
```markdown
# Сводный отчёт по агентным системам

## Дата: $(date +%Y-%m-%d)

## Найдено проектов: X

### Проект 1: [название]
- **Расположение**: /path/to/project
- **Размер**: X строк кода
- **Архитектура**: описание
- **Соответствие Манифесту**: X/20
- **Сильные стороны**: ...
- **Слабые стороны**: ...

### Проект 2: [название]
...

## Общие выводы
...

## Рекомендации
...
```

**B. `analysis/<project_name>_detailed.md`** - детальный анализ каждого проекта

**C. `code/improvements.py`** - production-ready код улучшений

### 9. GITHUB PUSH

После создания всех отчётов:

```bash
cd /root/agent-analysis

# Добавь все файлы
git add .

# Создай коммит с датой
git commit -m "Analysis update: $(date +%Y-%m-%d_%H-%M)

- Analyzed X projects
- Found Y gaps
- Generated Z improvements
- Compared with Manifest 5.0.0"

# Пуш в GitHub
git push origin main
```

### 10. СОЗДАНИЕ GITHUB ISSUES ДЛЯ УЛУЧШЕНИЙ

Для каждого критического улучшения создай issue:

```bash
# Создать issue для CRITICAL улучшения
curl -X POST \
  -H "Authorization: token ghp_YOUR_GITHUB_TOKEN_HERE" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/lidenal85-blip/agent-analysis/issues \
  -d '{
    "title": "CRITICAL: Implement Middleware Hooks (6 points)",
    "body": "## Проблема\nВ текущей реализации отсутствуют 6 middleware hooks из LangChain v1.0.\n\n## Решение\nРеализовать: before_agent, before_model, modify_model_request, after_model, wrap_tool_call, after_tool_call\n\n## Код\nСм. code/middleware_hooks.py\n\n## Сложность\n4/10\n\n## Приоритет\nCRITICAL",
    "labels": ["enhancement", "critical"]
  }'
```

## ТРЕБОВАНИЯ

### Что делать:
1. ✅ Найти ВСЕ агентные проекты на сервере
2. ✅ Проанализировать GitHub репозитории
3. ✅ Сравнить с Манифестом 5.0.0
4. ✅ Создать детальные отчёты
5. ✅ Написать production-ready код улучшений
6. ✅ Запушить всё в GitHub
7. ✅ Создать issues для критических улучшений

### Что НЕ делать:
- ❌ Не писать абстрактные идеи
- ❌ Не создавать файлы без кода
- ❌ Не забывать пушить в GitHub

### Формат отчётов:
- Markdown с таблицами
- Конкретные примеры кода
- Оценки сложности (1-10)
- Приоритеты (CRITICAL/HIGH/MEDIUM)

## НАЧНИ С ШАГА 1

Выполни все команды по порядку. После каждого шага проверяй результат.

В конце создай финальный отчёт `FINAL_REPORT.md` и запушь в GitHub.

ВАЖНО:
- Используй предоставленные GitHub credentials
- Пушь все результаты в репозиторий agent-analysis
- Создавай issues для критических улучшений
- Будь критичен — найди реальные проблемы

НАЧНИ!
