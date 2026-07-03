# 🧩 Core Library

**Path:** `/opt/leviathan_engine/core` | **Priority:** P0 🔴 Critical

## Overview

Shared library used by leviathan_engine and other services. Contains:
- `llm_factory.py` — Multi-LLM abstraction layer
- `key_pool.py` — API key management
- `orchestrator.py` — Agent orchestration
- `prompt_engine.py` — Prompt templating
- `event_bus.py` — Internal event system

## Components

| Module | Purpose |
|--------|---------|
| `base_agent.py` | Base agent interface |
| `llm_factory.py` | LLM provider abstraction |
| `key_pool.py` | Key rotation & pooling |
| `orchestrator.py` | Multi-agent orchestration |
| `prompt_engine.py` | Dynamic prompt generation |

## Tech Stack

| Component | Technology |
|-----------|------------|
| **Language** | Python 3.12 |
| **Design Pattern** | Factory, Strategy, Observer |

---

## Full Audit

[📊 Полный аудит проекта](../analysis/audit/core_audit/)
