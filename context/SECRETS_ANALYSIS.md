# АНАЛИЗ СИСТЕМЫ СЕКРЕТОВ
**Дата:** 2026-07-01

## Найденные секреты:

### Проект 1: Leviathan Engine
- **Файл:** /opt/leviathan_engine/agent_service/.env
- **Ключи:**
  - GEMINI_K1..K6: 6 ключей Gemini (AIzaSy...)
  - GROQ_K1..K5: 5 ключей Groq (gsk_...)
  - TG_BOT_TOKEN: 8604646197:AAFJM_c38cGBxICgUDWB-jEtgImUz0Fyo2M
  - GITHUB_TOKEN: ghp_YOUR_GITHUB_TOKEN_HERE
  - FAL_KEY: dfffce81-c079-490f-9e42-b0b787cf90e8:c0f435ad65001b04ee56dda925c50c1d
  - LEV_MCP_TOKEN: den4ik1985!
- **Всего:** 15 ключей

### Проект 2: Triton Agent
- **Файл:** /opt/triton_agent/.env
- **Ключи:**
  - TELEGRAM_BOT_TOKEN: 8772720473:AAHB_z6ZLfR9loQsHdi-yYe5f3bjHWUv2y8
- **Всего:** 1 ключ

### Проект 3: Pelleto AI
- **Файл:** /opt/pelleto-ai/.env
- **Ключи:**
  - GEMINI_K3..K6, K11..K14: 8 ключей Gemini
  - SECRET_KEY: 5e2d8f790b7aa1e7178f4794dd5492dc7f778aa79cdc6848d6fb068932bc6918
  - MASTER_KEY_HASH: $2b$12$...
  - TELEGRAM_BOT_TOKEN: 8968834167:AAEtIfylApWgWcXwuV7JmoeGMtMEeqdaZ_8
- **Всего:** 11 ключей (8 дублируются с Leviathan)

### Дубликаты:
- **GEMINI_K1 (AIzaSyB4JpiP...)** — в Leviathan и Pelleto
- **GEMINI_K3 (AIzaSyAZ0P9...)** — в Leviathan и Pelleto
- **GEMINI_K4 (AIzaSyDyJi...)** — в Leviathan и Pelleto
- **GEMINI_K5 (AIzaSyBgUw...)** — в Leviathan и Pelleto
- **GEMINI_K6 (AIzaSyAoUA...)** — в Leviathan и Pelleto

### Hardcoded ключи в коде:
- **bot.py** (125KB) — TG_TOKEN, API_KEY
- **production_gateway.py** — Groq API key, Gemini API key прямо в коде!

## Проблемы:
1. ❌ **Дублирование ключей** — Gemini ключи в 2+ проектах
2. ❌ **Ключи в коде (hardcoded)** — production_gateway.py, bot.py
3. ❌ **Нет ротации ключей** — все ключи статичны
4. ❌ **Нет аудита доступа** — кто когда использовал ключ
5. ❌ **Нет шифрования at rest** — .env файлы в открытом виде
6. ❌ **GitHub token в .env** — доступ к GitHub
7. ❌ **TG токены не защищены** — 3 разных токена

## Решение:
Создать централизованный Secrets Vault API на порту 8400
