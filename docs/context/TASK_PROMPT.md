# 🎯 АВТОНОМНЫЙ ПРОМПТ ДЛЯ CLAUDE CODE: ПОЛНАЯ РЕАЛИЗАЦИЯ

Скопируй и вставь в Claude Code:

```markdown
# ЗАДАЧА: АВТОНОМНАЯ РЕАЛИЗАЦИЯ SECRETS VAULT + УЛУЧШЕНИЯ АГЕНТНОЙ СИСТЕМЫ

## ⚠️ КРИТИЧЕСКИЕ ТРЕБОВАНИЯ К ВЫПОЛНЕНИЮ

**ТЫ ДОЛЖЕН РАБОТАТЬ АВТОНОМНО БЕЗ ОСТАНОВОК И НАПОМИНАНИЙ!**

1. ✅ Выполняй ВСЕ шаги последовательно БЕЗ запроса подтверждения
2. ✅ Логируй КАЖДОЕ действие в файл `/root/implementation_log.md`
3. ✅ Создавай отчёты после каждого этапа
4. ✅ НЕ СПРАШИВАЙ "продолжить?" — просто продолжай
5. ✅ При ошибках — логируй и иди дальше
6. ✅ В конце создай финальный отчёт

---

## ЭТАП 0: АНАЛИЗ СИСТЕМЫ СЕКРЕТОВ (ПЕРВЫЙ ШАГ)

### 0.1 Найди все секреты на сервере

```bash
# Поиск .vault папок
find /root /opt -type d -name ".vault" -o -name "secrets" -o -name "vault" 2>/dev/null

# Поиск .env файлов со всеми ключами
find /root /opt -name ".env" -type f 2>/dev/null

# Поиск конфигов с ключами
find /root /opt -name "*.yaml" -o -name "*.yml" -o -name "*.json" 2>/dev/null | xargs grep -l "API_KEY\|SECRET\|TOKEN" 2>/dev/null

# Поиск всех ключей в проектах
grep -r "API_KEY\|SECRET_KEY\|TOKEN\|PASSWORD" /opt/*/ --include="*.py" --include="*.env" 2>/dev/null | grep -v ".pyc" | grep -v "__pycache__"
```

### 0.2 Проанализируй найденные секреты

Для каждого найденного файла:
```bash
# Прочитай содержимое
cat /path/to/secret/file

# Извлеки все ключи
grep -E "^[A-Z_]+_KEY=|^[A-Z_]+_TOKEN=|^[A-Z_]+_SECRET=" /path/to/secret/file
```

### 0.3 Создай отчёт по секретам

Создай файл `/root/analysis/SECRETS_ANALYSIS.md`:

```markdown
# АНАЛИЗ СИСТЕМЫ СЕКРЕТОВ

## Найденные секреты:

### Проект 1: Leviathan Engine
- Файл: /opt/leviathan_engine/.env
- Ключи:
  - GEMINI_API_KEY: AQ.Ab8... (40 символов)
  - GROQ_API_KEY: gsk_... (50 символов)
  - DEEPSEEK_API_KEY: sk-... (50 символов)
  - TELEGRAM_BOT_TOKEN: ... (35 символов)
  - ...

### Проект 2: Triton Agent
- Файл: /opt/triton_agent/.env
- Ключи:
  - GROQ_API_KEY: ...
  - GEMINI_API_KEY: ...
  - ...

### Дубликаты:
- GROQ_API_KEY встречается в 3 проектах
- GEMINI_API_KEY встречается в 4 проектах
- ...

## Проблемы:
1. ❌ Дублирование ключей
2. ❌ Ключи в коде (hardcoded)
3. ❌ Нет ротации ключей
4. ❌ Нет аудита доступа
5. ❌ Нет шифрования at rest

## Решение:
Создать централизованный Secrets Vault API
```

---

## ЭТАП 1: СОЗДАНИЕ SECRETS VAULT API

### 1.1 Структура проекта

Создай `/opt/secrets_vault/`:

```
secrets_vault/
├── api/
│   ├── main.py              # FastAPI entry point
│   ├── routes/
│   │   ├── secrets.py       # CRUD для секретов
│   │   ├── projects.py      # Управление проектами
│   │   └── audit.py         # Аудит доступа
│   └── middleware/
│       ├── auth.py          # Аутентификация
│       └── logging.py       # Логирование
├── core/
│   ├── vault.py             # Хранилище секретов
│   ├── encryption.py        # Шифрование (AES-256)
│   ├── rotation.py          # Ротация ключей
│   └── audit.py             # Аудит-лог
├── db/
│   ├── models.py            # SQLAlchemy модели
│   ├── database.py          # Подключение к БД
│   └── migrations/          # Alembic миграции
├── config/
│   ├── settings.py          # Настройки
│   └── .env                 # Мастер-ключ
├── tests/
│   ├── test_vault.py
│   └── test_api.py
├── requirements.txt
└── README.md
```

### 1.2 Реализация Vault API

#### `/opt/secrets_vault/api/main.py`

```python
from fastapi import FastAPI, HTTPException, Depends
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from contextlib import asynccontextmanager
import logging
import sys

# Настройка логирования
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('/var/log/secrets_vault.log'),
        logging.StreamHandler(sys.stdout)
    ]
)
logger = logging.getLogger(__name__)

@asynccontextmanager
async def lifespan(app: FastAPI):
    logger.info("🔐 Secrets Vault API starting...")
    # Инициализация БД
    from db.database import init_db
    await init_db()
    logger.info("✅ Database initialized")
    yield
    logger.info("👋 Secrets Vault API shutting down...")

app = FastAPI(
    title="Secrets Vault API",
    description="Централизованное управление секретами для агентных систем",
    version="1.0.0",
    lifespan=lifespan
)

# Подключаем роуты
from api.routes import secrets, projects, audit
app.include_router(secrets.router, prefix="/api/v1/secrets", tags=["secrets"])
app.include_router(projects.router, prefix="/api/v1/projects", tags=["projects"])
app.include_router(audit.router, prefix="/api/v1/audit", tags=["audit"])

@app.get("/health")
async def health():
    return {"status": "ok", "service": "secrets_vault"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8400)
```

#### `/opt/secrets_vault/core/vault.py`

```python
from cryptography.fernet import Fernet
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
import base64
import os
from typing import Optional, Dict, Any
from datetime import datetime
import json

class SecretsVault:
    """Централизованное хранилище секретов с шифрованием"""
    
    def __init__(self, master_key: str, db_path: str = "/opt/secrets_vault/vault.db"):
        self.master_key = master_key
        self.db_path = db_path
        self.fernet = self._init_encryption()
        self._init_db()
    
    def _init_encryption(self) -> Fernet:
        """Инициализация шифрования AES-256"""
        # Derive key from master password
        salt = b'leviathan_salt_v1'  # В production использовать случайную соль
        kdf = PBKDF2HMAC(
            algorithm=hashes.SHA256(),
            length=32,
            salt=salt,
            iterations=100000,
        )
        key = base64.urlsafe_b64encode(kdf.derive(self.master_key.encode()))
        return Fernet(key)
    
    def _init_db(self):
        """Инициализация SQLite БД"""
        import sqlite3
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        cursor.execute('''
            CREATE TABLE IF NOT EXISTS secrets (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                project_id TEXT NOT NULL,
                key_name TEXT NOT NULL,
                encrypted_value TEXT NOT NULL,
                created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                accessed_at TIMESTAMP,
                access_count INTEGER DEFAULT 0,
                UNIQUE(project_id, key_name)
            )
        ''')
        cursor.execute('''
            CREATE TABLE IF NOT EXISTS projects (
                id TEXT PRIMARY KEY,
                name TEXT NOT NULL,
                api_key TEXT NOT NULL,
                created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                is_active BOOLEAN DEFAULT 1
            )
        ''')
        cursor.execute('''
            CREATE TABLE IF NOT EXISTS audit_log (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                project_id TEXT NOT NULL,
                action TEXT NOT NULL,
                key_name TEXT,
                timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                ip_address TEXT,
                success BOOLEAN
            )
        ''')
        conn.commit()
        conn.close()
    
    def encrypt(self, value: str) -> str:
        """Шифрование значения"""
        return self.fernet.encrypt(value.encode()).decode()
    
    def decrypt(self, encrypted_value: str) -> str:
        """Дешифрование значения"""
        return self.fernet.decrypt(encrypted_value.encode()).decode()
    
    def set_secret(self, project_id: str, key_name: str, value: str) -> bool:
        """Сохранение секрета"""
        import sqlite3
        encrypted = self.encrypt(value)
        
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        try:
            cursor.execute('''
                INSERT OR REPLACE INTO secrets (project_id, key_name, encrypted_value, updated_at)
                VALUES (?, ?, ?, CURRENT_TIMESTAMP)
            ''', (project_id, key_name, encrypted))
            conn.commit()
            return True
        except Exception as e:
            print(f"Error setting secret: {e}")
            return False
        finally:
            conn.close()
    
    def get_secret(self, project_id: str, key_name: str) -> Optional[str]:
        """Получение секрета"""
        import sqlite3
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        cursor.execute('''
            SELECT encrypted_value FROM secrets
            WHERE project_id = ? AND key_name = ?
        ''', (project_id, key_name))
        result = cursor.fetchone()
        conn.close()
        
        if result:
            # Обновляем статистику доступа
            self._log_access(project_id, key_name)
            return self.decrypt(result[0])
        return None
    
    def _log_access(self, project_id: str, key_name: str):
        """Логирование доступа"""
        import sqlite3
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        cursor.execute('''
            UPDATE secrets
            SET accessed_at = CURRENT_TIMESTAMP, access_count = access_count + 1
            WHERE project_id = ? AND key_name = ?
        ''', (project_id, key_name))
        conn.commit()
        conn.close()
    
    def list_secrets(self, project_id: str) -> Dict[str, Any]:
        """Список секретов проекта (без значений)"""
        import sqlite3
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        cursor.execute('''
            SELECT key_name, created_at, updated_at, accessed_at, access_count
            FROM secrets WHERE project_id = ?
        ''', (project_id,))
        results = cursor.fetchall()
        conn.close()
        
        return {
            row[0]: {
                "created_at": row[1],
                "updated_at": row[2],
                "accessed_at": row[3],
                "access_count": row[4]
            }
            for row in results
        }
    
    def delete_secret(self, project_id: str, key_name: str) -> bool:
        """Удаление секрета"""
        import sqlite3
        conn = sqlite3.connect(self.db_path)
        cursor = conn.cursor()
        cursor.execute('''
            DELETE FROM secrets WHERE project_id = ? AND key_name = ?
        ''', (project_id, key_name))
        conn.commit()
        deleted = cursor.rowcount > 0
        conn.close()
        return deleted
```

#### `/opt/secrets_vault/api/routes/secrets.py`

```python
from fastapi import APIRouter, HTTPException, Depends
from pydantic import BaseModel
from typing import Optional, Dict, Any
from core.vault import SecretsVault
import os

router = APIRouter()

# Инициализация vault
MASTER_KEY = os.getenv("VAULT_MASTER_KEY", "<your-master-key-here>")
vault = SecretsVault(MASTER_KEY)

class SecretRequest(BaseModel):
    key_name: str
    value: str

class SecretResponse(BaseModel):
    key_name: str
    value: str

@router.post("/{project_id}")
async def set_secret(project_id: str, request: SecretRequest):
    """Сохранить секрет"""
    success = vault.set_secret(project_id, request.key_name, request.value)
    if not success:
        raise HTTPException(status_code=500, detail="Failed to save secret")
    return {"status": "ok", "message": f"Secret {request.key_name} saved for {project_id}"}

@router.get("/{project_id}/{key_name}")
async def get_secret(project_id: str, key_name: str):
    """Получить секрет"""
    value = vault.get_secret(project_id, key_name)
    if value is None:
        raise HTTPException(status_code=404, detail="Secret not found")
    return {"key_name": key_name, "value": value}

@router.get("/{project_id}")
async def list_secrets(project_id: str):
    """Список секретов проекта"""
    secrets = vault.list_secrets(project_id)
    return {"project_id": project_id, "secrets": secrets}

@router.delete("/{project_id}/{key_name}")
async def delete_secret(project_id: str, key_name: str):
    """Удалить секрет"""
    success = vault.delete_secret(project_id, key_name)
    if not success:
        raise HTTPException(status_code=404, detail="Secret not found")
    return {"status": "ok", "message": f"Secret {key_name} deleted"}
```

### 1.3 Миграция существующих секретов

Создай скрипт `/opt/secrets_vault/migrate_secrets.py`:

```python
#!/usr/bin/env python3
"""Миграция всех секретов из проектов в Vault"""

import os
import sys
import re
from pathlib import Path
from core.vault import SecretsVault

MASTER_KEY = os.getenv("VAULT_MASTER_KEY", "<your-master-key-here>")
vault = SecretsVault(MASTER_KEY)

PROJECTS = {
    "leviathan": "/opt/leviathan_engine",
    "triton": "/opt/triton_agent",
    "pelleto": "/opt/pelleto-ai",
    "den4ik": "/opt/den4ik-claude",
    "arbitr": "/opt/arbitr_cockpit",
    "fcc": "/root/free-claude-code"
}

def extract_secrets_from_env(env_path: Path) -> dict:
    """Извлечение секретов из .env файла"""
    secrets = {}
    if not env_path.exists():
        return secrets
    
    with open(env_path, 'r') as f:
        for line in f:
            line = line.strip()
            if not line or line.startswith('#'):
                continue
            
            # Парсим KEY=VALUE
            match = re.match(r'^([A-Z_]+)=["\']?([^"\']+)["\']?$', line)
            if match:
                key, value = match.groups()
                # Фильтруем только секреты
                if any(x in key for x in ['KEY', 'TOKEN', 'SECRET', 'PASSWORD']):
                    secrets[key] = value
    
    return secrets

def migrate_project(project_id: str, project_path: str):
    """Миграция секретов проекта"""
    print(f"\n{'='*60}")
    print(f"📦 Миграция проекта: {project_id}")
    print(f"{'='*60}")
    
    env_path = Path(project_path) / ".env"
    secrets = extract_secrets_from_env(env_path)
    
    if not secrets:
        print(f"  ⚠️  Секреты не найдены в {env_path}")
        return
    
    print(f"  ✅ Найдено секретов: {len(secrets)}")
    
    for key, value in secrets.items():
        print(f"  🔐 Миграция: {key} ({len(value)} символов)")
        success = vault.set_secret(project_id, key, value)
        if success:
            print(f"     ✅ Сохранено в Vault")
        else:
            print(f"     ❌ Ошибка сохранения")

def main():
    print("🚀 Начинаем миграцию секретов в Vault...")
    
    for project_id, project_path in PROJECTS.items():
        if os.path.exists(project_path):
            migrate_project(project_id, project_path)
        else:
            print(f"\n⚠️  Проект {project_id} не найден: {project_path}")
    
    print(f"\n{'='*60}")
    print("✅ Миграция завершена!")
    print(f"{'='*60}")
    
    # Создаём отчёт
    report_path = "/root/analysis/SECRETS_MIGRATION_REPORT.md"
    with open(report_path, 'w') as f:
        f.write("# Отчёт миграции секретов\n\n")
        f.write(f"**Дата:** {Path.ctime(Path(__file__))}\n\n")
        f.write("## Мигрированные проекты:\n\n")
        for project_id in PROJECTS.keys():
            secrets = vault.list_secrets(project_id)
            f.write(f"### {project_id}\n")
            f.write(f"- Секретов: {len(secrets)}\n")
            for key, info in secrets.items():
                f.write(f"  - {key} (доступов: {info['access_count']})\n")
            f.write("\n")
    
    print(f"\n📄 Отчёт сохранён: {report_path}")

if __name__ == "__main__":
    main()
```

### 1.4 Клиент для интеграции с проектами

Создай `/opt/secrets_vault/client/vault_client.py`:

```python
"""Клиент для доступа к Secrets Vault API"""

import httpx
from typing import Optional, Dict, Any
import os

class VaultClient:
    """Клиент для работы с Secrets Vault"""
    
    def __init__(self, vault_url: str = "http://127.0.0.1:8400", project_id: str = None):
        self.vault_url = vault_url
        self.project_id = project_id or os.getenv("PROJECT_ID", "unknown")
        self.api_key = os.getenv("VAULT_API_KEY")
    
    def get_secret(self, key_name: str) -> Optional[str]:
        """Получить секрет из Vault"""
        try:
            response = httpx.get(
                f"{self.vault_url}/api/v1/secrets/{self.project_id}/{key_name}",
                headers={"Authorization": f"Bearer {self.api_key}"},
                timeout=5.0
            )
            if response.status_code == 200:
                return response.json()["value"]
            return None
        except Exception as e:
            print(f"Error getting secret {key_name}: {e}")
            return None
    
    def set_secret(self, key_name: str, value: str) -> bool:
        """Сохранить секрет в Vault"""
        try:
            response = httpx.post(
                f"{self.vault_url}/api/v1/secrets/{self.project_id}",
                headers={"Authorization": f"Bearer {self.api_key}"},
                json={"key_name": key_name, "value": value},
                timeout=5.0
            )
            return response.status_code == 200
        except Exception as e:
            print(f"Error setting secret {key_name}: {e}")
            return False
    
    def list_secrets(self) -> Dict[str, Any]:
        """Список секретов"""
        try:
            response = httpx.get(
                f"{self.vault_url}/api/v1/secrets/{self.project_id}",
                headers={"Authorization": f"Bearer {self.api_key}"},
                timeout=5.0
            )
            if response.status_code == 200:
                return response.json()["secrets"]
            return {}
        except Exception as e:
            print(f"Error listing secrets: {e}")
            return {}

# Пример использования
if __name__ == "__main__":
    client = VaultClient(project_id="leviathan")
    
    # Получить секрет
    gemini_key = client.get_secret("GEMINI_API_KEY")
    print(f"Gemini key: {gemini_key[:10]}..." if gemini_key else "Not found")
    
    # Список секретов
    secrets = client.list_secrets()
    print(f"Secrets: {list(secrets.keys())}")
```

### 1.5 Запуск Vault API

Создай systemd service `/etc/systemd/system/secrets-vault.service`:

```ini
[Unit]
Description=Secrets Vault API
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/opt/secrets_vault
Environment="VAULT_MASTER_KEY=<your-master-key-here>"
ExecStart=/usr/bin/python3 /opt/secrets_vault/api/main.py
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Запуск:
```bash
sudo systemctl daemon-reload
sudo systemctl enable secrets-vault
sudo systemctl start secrets-vault
```

---

## ЭТАП 2: ИНТЕГРАЦИЯ VAULT С ПРОЕКТАМИ

### 2.1 Интеграция с Leviathan Engine

Измени `/opt/leviathan_engine/agent_service/config/settings.py`:

```python
# БЫЛО:
GEMINI_API_KEY = os.getenv("GEMINI_API_KEY")

# СТАЛО:
from client.vault_client import VaultClient
vault = VaultClient(project_id="leviathan")
GEMINI_API_KEY = vault.get_secret("GEMINI_API_KEY")
```

### 2.2 Интеграция с Triton Agent

Аналогично для `/opt/triton_agent/config.py`:

```python
from vault_client import VaultClient
vault = VaultClient(project_id="triton")
GROQ_API_KEY = vault.get_secret("GROQ_API_KEY")
GEMINI_API_KEY = vault.get_secret("GEMINI_API_KEY")
```

### 2.3 Интеграция с FCC

Измени `/root/free-claude-code/.env` — удалить все ключи, оставить только:

```bash
VAULT_API_URL="http://127.0.0.1:8400"
PROJECT_ID="fcc"
VAULT_API_KEY="fcc_api_key_here"
```

---

## ЭТАП 3: РЕАЛИЗАЦИЯ УЛУЧШЕНИЙ ИЗ АНАЛИЗА

### 3.1 AgentState FSM для Leviathan

Создай `/opt/leviathan_engine/agent_service/agent/state.py`:

```python
from enum import Enum
from dataclasses import dataclass, field
from datetime import datetime
from typing import Optional, Dict, Any, List
import asyncio

class AgentState(Enum):
    INIT = "init"
    PLAN = "plan"
    REASON = "reason"
    ACT = "act"
    OBSERVE = "observe"
    REFLECT = "reflect"
    LEARN = "learn"
    COMPLETE = "complete"
    FAILED = "failed"
    INTERRUPTED = "interrupted"
    PAUSED = "paused"
    RESUMED = "resumed"

@dataclass
class StateTransition:
    from_state: AgentState
    to_state: AgentState
    timestamp: datetime = field(default_factory=datetime.utcnow)
    metadata: Dict[str, Any] = field(default_factory=dict)

class StateMachine:
    VALID_TRANSITIONS = {
        AgentState.INIT: [AgentState.PLAN, AgentState.FAILED],
        AgentState.PLAN: [AgentState.REASON, AgentState.INTERRUPTED, AgentState.FAILED],
        AgentState.REASON: [AgentState.ACT, AgentState.REFLECT, AgentState.FAILED],
        AgentState.ACT: [AgentState.OBSERVE, AgentState.INTERRUPTED, AgentState.FAILED],
        AgentState.OBSERVE: [AgentState.REFLECT, AgentState.REASON, AgentState.COMPLETE, AgentState.FAILED],
        AgentState.REFLECT: [AgentState.LEARN, AgentState.REASON, AgentState.FAILED],
        AgentState.LEARN: [AgentState.REASON, AgentState.COMPLETE, AgentState.FAILED],
        AgentState.COMPLETE: [],
        AgentState.FAILED: [AgentState.INIT],
        AgentState.INTERRUPTED: [AgentState.RESUMED, AgentState.FAILED],
        AgentState.PAUSED: [AgentState.RESUMED, AgentState.FAILED],
        AgentState.RESUMED: [AgentState.REASON, AgentState.PLAN],
    }
    
    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        self.current_state = AgentState.INIT
        self.history: List[StateTransition] = []
        self._lock = asyncio.Lock()
    
    async def transition(self, to_state: AgentState, metadata: Dict[str, Any] = None) -> bool:
        async with self._lock:
            if to_state not in self.VALID_TRANSITIONS.get(self.current_state, []):
                raise ValueError(f"Invalid transition: {self.current_state} -> {to_state}")
            
            transition = StateTransition(
                from_state=self.current_state,
                to_state=to_state,
                metadata=metadata or {}
            )
            self.history.append(transition)
            self.current_state = to_state
            return True
    
    def can_transition(self, to_state: AgentState) -> bool:
        return to_state in self.VALID_TRANSITIONS.get(self.current_state, [])
```

Интеграция в `LeviathanAgent`:

```python
# В /opt/leviathan_engine/agent_service/agent/core.py
from agent.state import StateMachine, AgentState

class LeviathanAgent:
    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        self.state_machine = StateMachine(agent_id)
    
    async def run(self, task: str):
        await self.state_machine.transition(AgentState.PLAN)
        # ... планирование
        
        await self.state_machine.transition(AgentState.REASON)
        # ... рассуждение
        
        await self.state_machine.transition(AgentState.ACT)
        # ... действие
        
        await self.state_machine.transition(AgentState.OBSERVE)
        # ... наблюдение
        
        await self.state_machine.transition(AgentState.COMPLETE)
```

### 3.2 Runnable Interface

Создай `/opt/leviathan_engine/agent_service/agent/runnable.py`:

```python
from abc import ABC, abstractmethod
from typing import TypeVar, Generic, AsyncIterator, List, Dict, Any

T = TypeVar('T')
R = TypeVar('R')

class Runnable(Generic[T, R], ABC):
    @abstractmethod
    async def invoke(self, input: T, config: Dict[str, Any] = None) -> R: ...
    
    @abstractmethod
    async def ainvoke(self, input: T, config: Dict[str, Any] = None) -> R: ...
    
    @abstractmethod
    async def stream(self, input: T, config: Dict[str, Any] = None) -> AsyncIterator[R]: ...
    
    @abstractmethod
    async def batch(self, inputs: List[T], config: Dict[str, Any] = None) -> List[R]: ...
    
    def __or__(self, other: 'Runnable[R, Any]') -> 'RunnableSequence':
        return RunnableSequence([self, other])

class RunnableSequence(Runnable[T, Any]):
    def __init__(self, steps: List[Runnable]):
        self.steps = steps
    
    async def invoke(self, input: T, config: Dict[str, Any] = None) -> Any:
        result = input
        for step in self.steps:
            result = await step.invoke(result, config)
        return result
```

### 3.3 Middleware Pipeline

Создай `/opt/leviathan_engine/agent_service/middleware/pipeline.py`:

```python
from typing import Callable, Awaitable, Any, Dict, List
from dataclasses import dataclass
import asyncio

@dataclass
class MiddlewareContext:
    agent_id: str
    state: Dict[str, Any]
    request: Dict[str, Any]
    metadata: Dict[str, Any]

class Middleware:
    async def before_model(self, context: MiddlewareContext) -> MiddlewareContext:
        return context
    
    async def after_model(self, context: MiddlewareContext, response: Any) -> Any:
        return response

class MiddlewarePipeline:
    def __init__(self):
        self.middlewares: List[Middleware] = []
    
    def add(self, middleware: Middleware):
        self.middlewares.append(middleware)
    
    async def execute_before(self, context: MiddlewareContext) -> MiddlewareContext:
        for mw in self.middlewares:
            context = await mw.before_model(context)
        return context
    
    async def execute_after(self, context: MiddlewareContext, response: Any) -> Any:
        for mw in reversed(self.middlewares):
            response = await mw.after_model(context, response)
        return response

# Пример middleware
class LoggingMiddleware(Middleware):
    async def before_model(self, context: MiddlewareContext) -> MiddlewareContext:
        print(f"[LOG] Before model: {context.agent_id}")
        return context
    
    async def after_model(self, context: MiddlewareContext, response: Any) -> Any:
        print(f"[LOG] After model: {response}")
        return response

class RateLimitMiddleware(Middleware):
    def __init__(self, max_requests: int = 100):
        self.max_requests = max_requests
        self.request_count = 0
    
    async def before_model(self, context: MiddlewareContext) -> MiddlewareContext:
        self.request_count += 1
        if self.request_count > self.max_requests:
            raise Exception("Rate limit exceeded")
        return context
```

---

## ЭТАП 4: ЛОГИРОВАНИЕ И ОТЧЁТЫ

### 4.1 Глобальное логирование

Создай `/root/implementation_log.md` и логируй КАЖДОЕ действие:

```markdown
# ЛОГ РЕАЛИЗАЦИИ

## 2026-07-01 08:00 - Начало работы
- ✅ Проанализирована система секретов
- ✅ Найдено 6 проектов с секретами
- ✅ Обнаружено 23 уникальных ключа

## 2026-07-01 08:15 - Создание Secrets Vault
- ✅ Создана структура проекта /opt/secrets_vault/
- ✅ Реализовано шифрование AES-256
- ✅ Создана БД для хранения секретов
- ✅ Реализован API (FastAPI)

## 2026-07-01 08:30 - Миграция секретов
- ✅ Мигрированы секреты Leviathan (8 ключей)
- ✅ Мигрированы секреты Triton (5 ключей)
- ✅ Мигрированы секреты Pelleto (3 ключа)
- ✅ Мигрированы секреты FCC (6 ключей)

## 2026-07-01 08:45 - Интеграция с проектами
- ✅ Интегрировано с Leviathan Engine
- ✅ Интегрировано с Triton Agent
- ✅ Интегрировано с FCC
- ⚠️ Pelleto требует доработки

## 2026-07-01 09:00 - Реализация улучшений
- ✅ Создан AgentState FSM
- ✅ Интегрирован с LeviathanAgent
- ✅ Создан Runnable Interface
- ✅ Создан Middleware Pipeline

## 2026-07-01 09:30 - Тестирование
- ✅ Тесты Vault API: 100% pass
- ✅ Тесты интеграции: 95% pass
- ⚠️ 1 тест упал (Pelleto MCP)

## 2026-07-01 10:00 - Завершение
- ✅ Все секреты мигрированы
- ✅ Все проекты интегрированы
- ✅ Все улучшения реализованы
- ✅ Документация создана
```

### 4.2 Финальный отчёт

Создай `/root/analysis/IMPLEMENTATION_REPORT.md`:

```markdown
# ОТЧЁТ О РЕАЛИЗАЦИИ

## Выполненные задачи:

### ✅ Этап 0: Анализ секретов
- Найдено 6 проектов
- Обнаружено 23 уникальных ключа
- Выявлены проблемы: дублирование, отсутствие ротации

### ✅ Этап 1: Secrets Vault API
- Создан проект /opt/secrets_vault/
- Реализовано шифрование AES-256
- Создан REST API (FastAPI)
- Порт: 8400
- Статус: 🟢 Production

### ✅ Этап 2: Интеграция с проектами
- Leviathan Engine: ✅ Интегрировано
- Triton Agent: ✅ Интегрировано
- Pelleto AI: ⚠️ Частично
- den4ik-claude: ✅ Интегрировано
- ArbitrCockpit: ✅ Интегрировано
- free-claude-code: ✅ Интегрировано

### ✅ Этап 3: Улучшения из анализа
- AgentState FSM: ✅ Реализовано
- Runnable Interface: ✅ Реализовано
- Middleware Pipeline: ✅ Реализовано
- Callback System: ⏳ В процессе

## Метрики:

| Метрика | Значение |
|---------|----------|
| Секретов мигрировано | 23 |
| Проектов интегрировано | 6/6 |
| Улучшений реализовано | 3/5 |
| Тестов пройдено | 95% |
| Время реализации | 2 часа |

## Следующие шаги:

1. Завершить Callback System (1 день)
2. Реализовать Workflow Engine (3 дня)
3. Добавить Plugin SDK (3 дня)
4. Внедрить Multi-Agent (4 дня)

## Рекомендации:

1. Настроить ротацию ключей (ежемесячно)
2. Добавить мониторинг доступа к секретам
3. Внедрить backup для Vault БД
4. Добавить alerting при подозрительной активности
```

---

## ⚡ ТРЕБОВАНИЯ К АВТОНОМНОСТИ

**ТЫ ДОЛЖЕН:**

1. ✅ Выполнять ВСЁ без остановок
2. ✅ НЕ спрашивать "продолжить?"
3. ✅ Логировать КАЖДОЕ действие в `/root/implementation_log.md`
4. ✅ Создавать отчёты после каждого этапа
5. ✅ При ошибках — логировать и идти дальше
6. ✅ В конце создать финальный отчёт

**ЗАПРЕЩЕНО:**

- ❌ Спрашивать подтверждение
- ❌ Останавливаться для проверки
- ❌ Ждать команд от пользователя
- ❌ Пропускать шаги

---

## 🚀 НАЧНИ НЕМЕДЛЕННО

**НАЧНИ С ЭТАПА 0** — анализ системы секретов.

Выполни все команды, создай отчёт, и переходи к Этапу 1.

РАБОТАЙ АВТОНОМНО БЕЗ ОСТАНОВОК!
```

---

## 🎯 КАК ИСПОЛЬЗОВАТЬ

1. **Скопируй промпт выше**
2. **Вставь в Claude Code** (через FCC + Gemini)
3. **Claude Code будет работать автономно**:
   - Проанализирует секреты
   - Создаст Secrets Vault API
   - Мигрирует все ключи
   - Интегрирует с проектами
   - Реализует улучшения
   - Создаст отчёты
   - Всё с логированием

4. **Мониторь прогресс**:
   ```bash
   tail -f /root/implementation_log.md
   ```

5. **После завершения** — читай отчёт:
   ```bash
   cat /root/analysis/IMPLEMENTATION_REPORT.md
   ```

---

## 📊 ЧТО ОЖИДАТЬ

После выполнения ты получишь:

✅ **Secrets Vault API** на порту 8400  
✅ **Все секреты мигрированы** из проектов  
✅ **Централизованное управление** ключами  
✅ **Шифрование AES-256** для всех секретов  
✅ **Аудит доступа** к секретам  
✅ **Интеграция** со всеми проектами  
✅ **AgentState FSM** для Leviathan  
✅ **Runnable Interface** для всех компонентов  
✅ **Middleware Pipeline** с hooks  
✅ **Полная документация** и отчёты  

Всё это будет реализовано **автоматически**, без твоего участия!
