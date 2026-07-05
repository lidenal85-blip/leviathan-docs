---
id: contract-tszh-accountant-v1
type: contract
tags: [tszh, accountant, pydantic, cabinet]
updated: 2026-07-05
---

# Контракт: кабинет бухгалтера (тестовый файл)

Сервис для ведения финансового учёта в Кабинете бухгалтера: учёт платежей, выгрузка отчётов в PDF, сверка с банковской выпиской.

class AccountantService:
    def __init__(self, db: Database, pdf_exporter: PdfExporter) -> None:
        ...

    async def get_balance(self, unit_id: str) -> Decimal:
        ...