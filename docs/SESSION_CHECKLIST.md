# ✅ Anti-Chaos Protocol — Session Checklist

> Based on MASTER_ROADMAP.md §5 — обязательный чек-лист для каждой сессии

## Before Any Action

### §5.1 — Verify, Don't Trust

- [ ] Выполнить `nproc` — проверить реальное кол-во CPU
- [ ] Выполнить `free -h` — проверить реальную RAM
- [ ] Выполнить `df -h /` — проверить диск
- [ ] Выполнить `uptime` — проверить load average
- [ ] Сверить метрики с MASTER_ROADMAP.md §1.2
- [ ] NOTA: Предыдущие отчёты могут содержать ошибки — всегда проверять напрямую

### §5.2 — Secrets Safety

- [ ] Проверить `~/.gitconfig` на наличие токенов
- [ ] Проверить `~/.env` на наличие секретов
- [ ] Убедиться, что `secrets-vault` (:8400) запущен:
  ```bash
  systemctl is-active secrets-vault
  ```
- [ ] Перед любым `git push` выполнить поиск секретов:
  ```bash
  git diff --cached | grep -iE 'api.?key|secret|token|password|sk-[a-zA-Z0-9]{20,}' && echo "⚠️ SECRETS FOUND!"
  ```

### §5.3 — Code Path Verification

- [ ] Убедиться, что изменяемый путь существует:
  ```bash
  ls -la <path>
  ```
- [ ] Проверить, что сервис действительно использует этот путь:
  ```bash
  systemctl cat <service> | grep -i path
  ```
- [ ] Запустить тесты (если есть):
  ```bash
  cd <project> && pytest || echo "No tests"
  ```

### §5.4 — One Change at a Time

- [ ] Не менять всю модель из-за одного сообщения пользователя
- [ ] Каждое изменение — отдельный коммит
- [ ] Каждая фаза требует независимой проверки (команды или тесты)

### §5.5 — MkDocs Build Check

- [ ] Выполнить `mkdocs build` и проверить WARNING/ERROR
- [ ] Проверить, что все ссылки в новых файлах резолвятся

---

*Source: /root/analysis/MASTER_ROADMAP.md §5*
