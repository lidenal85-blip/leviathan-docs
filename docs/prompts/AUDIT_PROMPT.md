# 🔍 LEVIATHAN AUDIT PROMPT (переиспользуемый)

Копируй это целиком в новую сессию (Claude Code, Buffy, или сюда же). Не пиши свой вариант с нуля — этот уже учитывает ошибки предыдущих аудитов.

## ПРАВИЛА (обязательно прочитать до начала)

1. **Перед любым выводом в отчёт — команда, которая это доказывает.** Ни одного "✅ готово" без команды рядом.
2. **Сервисы проверять и как живые, и как мёртвые:**
   ```bash
   systemctl list-units --type=service --state=running
   systemctl list-units --type=service --state=failed
   ```
   Вторая команда часто пропускается — именно так пропустили freebuffd в краш-лупе 24 часа.
3. **Перед созданием любого файла-отчёта:**
   ```bash
   ls -la /root/analysis/*.md | grep -iE 'final|status|master|report|audit'
   ls -la /root/analysis/audit/*.md
   ```
   Если похожий файл уже есть — дополни его, НЕ создавай новый. Сегодня в этой папке 102 файла, 9 из них — дубли друг друга.
4. **Сверяйся с MASTER_ROADMAP.md и MASTER_CHECKLIST.md ПЕРЕД началом**, не после. Цель аудита — обновить их, не создать параллельный.

## ЧТО ПРОВЕРИТЬ

```bash
# Ресурсы (не доверять цифрам старше пары часов)
free -h && nproc && df -h / | tail -1 && uptime

# Сервисы живые + мёртвые
systemctl list-units --type=service --state=running
systemctl list-units --type=service --state=failed

# Тесты — запустить, не только проверить наличие файлов
cd /opt/leviathan_engine && python3 -m pytest tests/ --tb=no -q

# Git — что реально в remote и нет ли токена в URL
git remote -v
git log --oneline -10

# Секреты — прямой греп после любой правки, не верить коммит-месседжам типа "clean, no tokens"
grep -rE '(ghp|gho)_[A-Za-z0-9]{15,}|AIza[A-Za-z0-9_-]{35}' . --exclude-dir=.git
```

## ФОРМАТ ВЫВОДА

Обнови `docs/plans/MASTER_CHECKLIST.md` на месте. Если нашёл что-то критичное, чего нет в MASTER_ROADMAP.md — допиши туда Раздел 6 (Журнал изменений), не создавай отдельный файл.
