# 🗄️ Memory SQLite Persistence — Отчёт

**Дата:** 2026-07-01
**Компонент:** Manifest 5.1.0 — Memory System V2

## Что сделано

`LongTermMemory` переведён с in-memory (`self._items: List`) на **SQLite**:

| Аспект | Было | Стало |
|--------|------|-------|
| Хранилище | `List[MemoryItem]` в RAM | SQLite (`db/long_term_memory.db`) |
| Поиск | `query.lower() in content.lower()` | `LIKE %query%` с индексом |
| Сортировка | Python sort() | SQL `ORDER BY importance DESC` |
| Счётчик | `len(self._items)` | `SELECT COUNT(*)` |
| Persistence | ❌ Терялось при перезапуске | ✅ Сохраняется на диск |
| Async safety | Lock не было | `asyncio.to_thread()` для sqlite3 |

## Индексы
- `idx_memories_imp` — по importance (DESC) для быстрого поиска важных
- `idx_memories_created` — по created_at (DESC) для списка

## Проблемы
- sqlite3 — синхронная библиотека, вызовы обёрнуты в `asyncio.to_thread()`
- Путь к БД: `db/long_term_memory.db` (относительный, зависит от CWD)

## Следующие шаги
- Добавить EpisodicMemory SQLite persistence
- Добавить SemanticMemory SQLite persistence
- Использовать `sqlite3.WAL` режим для конкурентного доступа
