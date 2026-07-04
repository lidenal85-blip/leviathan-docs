# 🎯 LEVIATHAN MASTER CHECKLIST
**Версия:** 1.0  
**Последнее обновление:** 2026-07-04 (статусы ниже), шапка — 2026-07-02 19:00 UTC  
**Обновил:** Qwen (исходный аудит), статусы — Claude (чат, 07-04)  
**Статус:** 🟡 КРИТИЧЕСКАЯ ФАЗА 0  

> ⚠️ **Цифры в таблице ниже устарели на 07-04.** Актуальные проверенные цифры — в `MASTER_ROADMAP.md` §1.2 и §6 (журнал от 07-04): диск 93% (было 87% на 07-03), сервисы `freebuffd`/`citrus_aura`/`lawu`/`arbitr` из этого чек-листа уже не в живом списке сервисов вовсе (см. `systemctl list-units`).

---

## 📊 ТЕКУЩЕЕ СОСТОЯНИЕ СИСТЕМЫ

| Метрика | Значение | Статус |
|---------|----------|--------|
| RAM usage | 66% (2.5/3.8 GiB) | 🟡 |
| **Swap usage** | **88% (4.4/5.0 GiB)** | 🔴 **КРИТИЧНО** |
| Load avg | 10+ (4 ядра) | 🔴 **КРИТИЧНО** |
| Disk usage | 80% (23/30G) | 🟡 |
| Python-процессов | 5+ (каждый 10% RAM) | 🔴 |
| Архитектура | 4/10 (фрагментированная) | 🔴 |

---

## 🚨 ФАЗА 0: ЭКСТРЕННОЕ СПАСЕНИЕ (Сегодня)

**Цель:** Остановить кровотечение ресурсов, освободить RAM

### Чек-лист Фазы 0

- [x] **0.1** 🔴 Остановить freebuffd (освободить 40% RAM)
  - **Статус:** ✅ Фактически мёртв, но НЕ через эту команду — сервис сам упал после 8634 краш-рестартов (~31ч), остановлен вручную 2026-07-02 20:46 UTC. `disable` НЕ выполнен — unit всё ещё `enabled`, стартует при ребуте и сразу упадёт снова (root cause: `ModuleNotFoundError: validator`, см. MASTER_ROADMAP §6, 07-04)
  - **Кто:** Claude (чат, 07-04) — только диагностика, не исправление
  - **Команда:** `sudo systemctl disable freebuffd` — всё ещё нужно выполнить, чтобы не воскрес на ребуте

- [ ] **0.2** 🔴 Убить все zombie процессы freebuff
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Команда:** `pkill -9 -f freebuff && sleep 2 && ps aux | grep freebuff`

- [ ] **0.3** 🔴 Очистить page cache
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Команда:** `echo 3 > /proc/sys/vm/drop_caches`

- [ ] **0.4** 🔴 Проверить состояние после очистки
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Команда:** `free -m && uptime && df -h`
  - **Критерий:** Swap < 50%, Load < 4

- [ ] **0.5** 🟡 Отключить проблемные сервисы
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Сервисы:** citrus_aura, lawu, arbitr (дубликаты)
  - **Команда:** `sudo systemctl disable citrus_aura lawu arbitr`

**Критерий завершения Фазы 0:**
- ✅ Swap < 50%
- ✅ Load < 4
- ✅ RAM usage < 60%
- ✅ Все критичные сервисы работают

---

## 🏗️ ФАЗА 1: КАРДИНАЛЬНАЯ АРХИТЕКТУРНАЯ ПЕРЕДЕЛКА (3-5 дней)

**Цель:** Консолидировать 5+ процессов в 1-2 процесса

### Чек-лист Фазы 1

- [ ] **1.1** 🔴 Создать структуру LAP (Leviathan Agent Platform)
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Команда:** `mkdir -p /opt/leviathan_engine/agent_service/lap/{kernel,agents,interfaces,shared}`

- [ ] **1.2** 🔴 Реализовать BaseAgent(Runnable) интерфейс
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/agents/base.py`

- [ ] **1.3** 🔴 Реализовать AgentRegistry (hot-plug)
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/agents/registry.py`

- [ ] **1.4** 🔴 Обернуть LeviathanAgent в BaseAgent
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/agents/general/`

- [ ] **1.5** 🔴 Обернуть Triton в BaseAgent
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/agents/sport_observer/`

- [ ] **1.6** 🔴 Обернуть den4ik-claude в BaseAgent (обёртка-адаптер!)
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/agents/tg_bot/`

- [ ] **1.7** 🔴 Создать Unified LLM Gateway
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/shared/llm_gateway.py`

- [ ] **1.8** 🔴 Создать Unified EventBus
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/shared/event_bus.py`

- [ ] **1.9** 🔴 Создать CLI entry point (lev-cli)
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/cli/main.py`

- [ ] **1.10** 🔴 Настроить systemd для LAP
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/etc/systemd/system/leviathan-platform.service`

**Критерий завершения Фазы 1:**
- ✅ Все 5 агентов зарегистрированы в AgentRegistry
- ✅ `lev-cli run agent <name>` работает для каждого агента
- ✅ Один systemd сервис (leviathan-platform)
- ✅ RAM usage < 40% (консолидация процессов)
- ✅ Load < 2

---

## 🧠 ФАЗА 2: ADR-002 KERNEL (5-7 дней)

**Цель:** Внедрить runtime decision-making engine

### Чек-лист Фазы 2

- [~] **2.1** 🔴 Реализовать ScenarioPolicy (Pydantic)
  - **Статус:** ⚠️ В процессе, НО по другому пути — фактически реализуется в `agent_service/core_bridge/scenario_loader.py` (помечено в коде как «ADR-002 Core Kernel Component 1/4»), НЕ в `lap/kernel/`. География путей из этого чек-листа не совпадает с тем, что реально пишется на диске. Файл по-прежнему не в git.
  - **Кто:** неизвестная сессия (не закоммичено, авторство не определить из git log)
  - **Файл (план):** `/opt/leviathan_engine/agent_service/lap/kernel/policy.py`
  - **Файл (факт):** `/opt/leviathan_engine/agent_service/core_bridge/scenario_loader.py`

- [ ] **2.2** 🔴 Реализовать DecisionEngine
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/kernel/decision_engine.py`

- [ ] **2.3** 🔴 Реализовать ExecutionLoop
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/kernel/execution_loop.py`

- [ ] **2.4** 🔴 Реализовать SafetyGuards
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/kernel/safety_guards.py`

- [ ] **2.5** 🔴 Реализовать ReplanningEngine
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/kernel/replanner.py`

- [ ] **2.6** 🔴 Создать первый ScenarioPolicy (freelance_seo.yaml)
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Файл:** `/opt/leviathan_engine/agent_service/lap/scenarios/freelance_seo.yaml`

- [ ] **2.7** 🔴 Тест: `lev-cli run scenario freelance --mode PRO`
  - **Статус:** ⏳ Ожидает
  - **Кто:** [свободно]
  - **Результат:** Полный цикл ADR-002 работает

**Критерий завершения Фазы 2:**
- ✅ ADR-002 kernel полностью реализован
- ✅ Runtime decision-making работает
- ✅ Replanning при неудачах работает
- ✅ Safety guards срабатывают
- ✅ Тесты ≥ 70%

---

## 🔄 ПРОТОКОЛ ОБНОВЛЕНИЯ ЧЕК-ЛИСТА

### Как обновлять:

1. **Найти задачу в чек-листе**
2. **Изменить статус:**
   - `[ ]` → `[~]` (в процессе)
   - `[~]` → `[x]` (выполнено)
   - `[x]` → `[!]` (ошибка/откат)

3. **Заполнить метаданные:**
   ```markdown
   - [x] **1.1** 🔴 Создать структуру LAP
     - **Статус:** ✅ Выполнено
     - **Кто:** Claude Code
     - **Когда:** 2026-07-02 19:30
     - **Команда:** `mkdir -p /opt/...`
     - **Результат:** Создано 5 директорий
   ```

4. **Обновить шапку документа:**
   ```markdown
   **Последнее обновление:** 2026-07-02 19:30 UTC  
   **Обновил:** Claude Code (реализация Фазы 1.1)
   ```

5. **Git commit:**
   ```bash
   cd /root/leviathan-docs/plans
   git add MASTER_CHECKLIST.md
   git commit -m "Phase 1.1: LAP structure created by Claude Code"
   git push origin main
   ```

---

**Готов начать?** Выбери задачу из чек-листа и выполни её! 🚀
```

---

## 🚀 КОМАНДЫ ДЛЯ СОЗДАНИЯ СТРУКТУРЫ

```bash
# 1. Создать папку
mkdir -p /root/leviathan-docs/{prompts,plans,reports,context}
cd /root/leviathan-docs

# 2. Скопировать MASTER_PROMPT.md
cat > prompts/MASTER_PROMPT.md << 'EOF'
[ВСТАВИТЬ СОДЕРЖИМОЕ MASTER_PROMPT.md ВЫШЕ]
EOF

# 3. Скопировать MASTER_CHECKLIST.md
cat > plans/MASTER_CHECKLIST.md << 'EOF'
[ВСТАВИТЬ СОДЕРЖИМОЕ MASTER_CHECKLIST.md ВЫШЕ]
EOF

# 4. Инициализировать git
git init
git config user.name "Leviathan Agent"
git config user.email "agent@leviathan.local"
git add .
git commit -m "Initial: Leviathan docs structure"

# 5. Создать remote (опционально)
git remote add origin https://lidenal85-blip:YOUR_TOKEN_HERE@github.com/lidenal85-blip/leviathan-docs.git
git push -u origin main
