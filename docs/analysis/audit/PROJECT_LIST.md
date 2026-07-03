# 📋 ПОЛНЫЙ СПИСОК ПРОЕКТОВ LEVIATHAN

**Дата:** 2026-07-03
**Сервер:** LEVIATHAN (78.17.24.96)
**Всего найдено:** 58 проектов

---

## 📊 СВОДНАЯ СТАТИСТИКА

| Категория | Количество |
|-----------|-----------|
| **Tier 1 (Критичные)** — systemd, активный git, продакшн | 18 |
| **Tier 2 (Важные)** — git, недавняя активность | 15 |
| **Tier 3 (Периферийные)** — старые, маленькие | 17 |
| **Tier 4 (Мёртвые)** — без коммитов, без README | 8 |
| **ИТОГО** | **58** |

| Тип | Количество |
|-----|-----------|
| systemd сервисы | 22 активных |
| Nginx сайты | 12 конфигураций |
| Git репозитории | ~35 |
| Cron задачи | 15+ |

---

## TIER 1: КРИТИЧНЫЕ (P0-P1) — 18 проектов

| # | Название | Путь | Тип | Размер | Сервис | Статус |
|---|----------|------|-----|--------|--------|--------|
| 1 | **leviathan_engine** | /opt/leviathan_engine/ | git | 144 MB | 4 systemd | 🟢 Active |
| 2 | **den4ik-claude** | /opt/den4ik-claude/ | git | 5K LOC | ✅ systemd | 🟢 Active |
| 3 | **arbitr_cockpit** | /opt/arbitr_cockpit/ | git | 8K LOC | ✅ systemd | 🟢 Active |
| 4 | **savdabot** | /opt/savdabot/ | git | 6K LOC | ✅ systemd | 🟢 Active |
| 5 | **diet_platform** | /opt/diet_platform/ | git | 5K LOC | ✅ systemd | 🟢 Active |
| 6 | **fashion-stylist** | /opt/fashion-stylist/ | — | 277 MB | ✅ systemd | 🟢 Active |
| 7 | **kwork_bot** | /opt/kwork_bot/ | git | 122 MB | ✅ systemd | 🟢 Active |
| 8 | **kwork_dispatcher** | /opt/kwork_dispatcher/ | git | — | ✅ systemd | 🟢 Active |
| 9 | **pelleto-ai** | /opt/pelleto-ai/ | git | 5K LOC | ✅ systemd | 🟡 Maintenance |
| 10 | **vocalplatform** | /opt/vocalplatform/ | — | — | 3 systemd | 🟡 Проблемы |
| 11 | **vocal_bot** | /opt/vocal_bot/ | git | 32K LOC | ✅ systemd | 🔴 Не работает |
| 12 | **triton_agent** | /opt/triton_agent/ | git | 3.9K LOC | ✅ (в engine) | 🟢 Active |
| 13 | **secrets_vault** | /opt/secrets_vault/ | git | 795 LOC | ❌ Нет | 🟢 Active |
| 14 | **free-claude-code** | /root/free-claude-code/ | git | 193 MB | fcc-server.service | 🟢 Active |
| 15 | **ai-interior-composer** | /root/ai-interior-composer/ | git | 391 MB | aic-api.service | 🟢 Active |
| 16 | **kinovibe** | /var/www/kinovibe/ | git | 202 MB | Nginx | 🟡 Разработка |
| 17 | **fri-messenger** | /opt/fri-messenger/ | git | 403 MB | fri-messenger.service | 🟢 Active |
| 18 | **leviathan-docs** | /opt/leviathan-docs/ | git | 7K LOC | MkDocs | 🟢 Active |

---

## TIER 2: ВАЖНЫЕ (P2) — 15 проектов

| # | Название | Путь | Тип | Размер | Статус |
|---|----------|------|-----|--------|--------|
| 19 | **voicestudio** | /var/www/voicestudio/ | Nginx | 64 MB | 🟡 Active |
| 20 | **outreach** | /var/www/outreach/ | Nginx | 50 MB | 🟡 Active |
| 21 | **f3platform** | /var/www/f3platform/ | git+Nginx | — | 🟡 Active |
| 22 | **leviathan_hub** | /var/www/leviathan_hub/ | git+Nginx | 7.8 MB | 🟡 Active |
| 23 | **citrus_aura** | /opt/citrus_aura/ | git | 5K LOC | citrus_aura.service | 🟡 Maintenance |
| 24 | **voice_studio** | /var/www/voice_studio/ | git | — | 🟡 Active |
| 25 | **agent-hub** | /opt/agent-hub/ | — | 141 LOC | agent-hub.service | 🔴 Dead (TODO) |
| 26 | **survey-finder** | /root/survey-finder/ | git | 4.1 MB | ❌ | 🟡 Active |
| 27 | **hh_sniper** | /opt/hh_sniper/ | git+Nginx | — | hh-sniper-api.service | 🟡 Active |
| 28 | **aic** | /var/www/aic/ | Nginx | — | ❌ | ❓ Unknown |
| 29 | **narrative** | /var/www/narrative/ | Nginx | — | ❌ | ❓ Unknown |
| 30 | **agrobot** | /opt/agrobot/ | git | 141 LOC | agrobot.service | 🟡 Maintenance |
| 31 | **media_factory** | /opt/media_factory/ | git | 3K LOC | ❌ | 🟡 Maintenance |
| 32 | **tg_channel_monitor** | /opt/tg_channel_monitor/ | — | — | ❌ | 🔴 Unknown |
| 33 | **freebuff** | /opt/freebuff/ + /root/freebuff/ | npm | — | freebuffd.service | 🟢 Active |

---

## TIER 3: ПЕРИФЕРИЙНЫЕ (P3) — 17 проектов

| # | Название | Путь | Тип | Размер | Статус |
|---|----------|------|-----|--------|--------|
| 34 | **tszh-bot** | /root/tszh-bot/ | git | 636 KB | Nginx | 🟡 Active |
| 35 | **leviathan-property** | /root/leviathan-property/ | git | 208 KB | ❌ | 🟡 Maintenance |
| 36 | **core** | /root/core/ | — | 2.6 MB | ❌ | ❓ Unknown |
| 37 | **registry** | /root/registry/ | — | 120 KB | ❌ | ❓ Unknown |
| 38 | **hq** | /opt/hq/ | git | — | ❌ | ❓ Unknown |
| 39 | **new_project** | /opt/new_project/ | git | — | ❌ | ❓ Unknown |
| 40 | **dle_billing_pro** | /opt/dle_billing_pro/ | git | 122 LOC | ❌ | ❓ Unknown |
| 41 | **telegram-agent-book** | /opt/telegram-agent-book/ | git | 1.1K LOC | ❌ | 🟡 Maintenance |
| 42 | **agent_output** | /opt/agent_output/ | git | — | ❌ | ❓ Unknown |
| 43 | **night_research** | /opt/night_research/ | — | — | ❌ | 🟡 Active (cron) |
| 44 | **claude-wake** | /opt/claude-wake/ | — | — | claude-watcher.service | 🟡 Active |
| 45 | **save_video** | /opt/save_video/ | — | — | ❌ | ❓ Unknown |
| 46 | **fl_dispatcher** | /opt/fl_dispatcher/ | — | 203 MB | ❌ | 🟡 Active (cron) |
| 47 | **flight-payment** | /opt/flight-payment/ | — | — | ❌ | ❓ Unknown |
| 48 | **hermes_agent** | /opt/hermes_agent_venv/ | — | 138 MB | ❌ | ❓ Unknown |
| 49 | **minusfabrika_api** | /opt/minusfabrika_api/ | — | — | ❌ | ❓ Unknown |
| 50 | **promtcomfort** | /opt/promtcomfort/ | — | — | ❌ | ❓ Unknown |

---

## TIER 4: МЁРТВЫЕ (P4) — 8 проектов

| # | Название | Путь | Тип | Размер | Статус |
|---|----------|------|-----|--------|--------|
| 51 | **dispatcher_simulator** | /opt/dispatcher_simulator/ | — | 646K LOC ❓ | 🔴 Dead (не git) |
| 52 | **backups** | /root/backups/ | — | 17 MB | 🔴 Архив |
| 53 | **cert** | /root/cert/ | — | 16 KB | 🔴 Сертификаты |
| 54 | **bin** | /root/bin/ | — | 196 KB | 🔴 Скрипты |
| 55 | **snap** | /root/snap/ | — | 4 KB | 🔴 Ссылки |
| 56 | **den4ik_hq** | /opt/den4ik_hq/ | git | — | 🔴 Flutter, не используется |
| 57 | **fashion-stylist (assets)** | /opt/fashion-stylist/ | — | 277 MB | 🟡 Active (сервис) |
| 58 | **html** | /var/www/html/ | — | — | 🔴 Статика Nginx |

---

## 📁 РАСПРЕДЕЛЕНИЕ ПО ДИРЕКТОРИЯМ

| Директория | Проектов |
|-----------|---------|
| /opt/ | 25 |
| /root/ | 13 |
| /var/www/ | 12 |
| /etc/ | ~15 systemd |
| /srv/ | 1 (ftp) |
| **ИТОГО** | **~58** |

---

## 🔗 ИСТОЧНИКИ ОБНАРУЖЕНИЯ

| Источник | Найдено проектов |
|----------|-----------------|
| ls -la /opt/ | 25+ |
| find .git | 35 |
| systemd | 22 сервиса |
| Nginx sites | 12 конфигов |
| cron | 15+ задач |
| find package.json/requirements... | 30+ маркеров |
| find Dockerfile | 5+ |

---
*Документ создан: 2026-07-03*
*Всего проектов: 58*
*Путь: /root/analysis/audit/PROJECT_LIST.md*
