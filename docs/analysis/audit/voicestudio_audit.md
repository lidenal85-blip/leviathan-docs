# 🎙️ АУДИТ: Voice Studio

**Путь:** /var/www/voicestudio/
**Размер:** 64 MB
**Tier:** 2 (Важный)
**Сервис:** Nginx
**Git:** ❌ Не проверен
**README:** ❌

## Стек
Python, FastAPI, Docker, Colab (colab_accounts.json!)

## Статус
🟡 Active — есть API, Dockerfile, .env

## 🚨 Проблемы безопасности
- **colab_accounts.json** — чувствительные данные в репозитории 🔴
- **colab_worker.ipynb** — Jupyter ноутбук с доступом
- **.env.bak.20260617_105934** — резервная копия секретов 🔴

## Оценка: 4/10 🔴 (из-за секретов)
