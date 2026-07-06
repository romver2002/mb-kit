---
status: draft
version: "1.0"
updated: 2026-07-05
verified_commit: ""
owner: ""
---

# Паттерны и анти-паттерны

Принятые способы решения типовых задач в {{PROJECT_NAME}}. Слои и границы —
[overview.md](overview.md); именование — [code-style.md](code-style.md).
Новый паттерн вводится через ADR ([../decisions/README.md](../decisions/README.md))
и после принятия добавляется в таблицу.

## Принятые паттерны

| Паттерн | Когда применять | Пример в коде |
|---|---|---|
| Repository | любой доступ к хранилищу: интерфейс в domain, реализация в infrastructure | TODO(template): путь к интерфейсу и к реализации |
| Use Case / Interactor | каждый пользовательский или системный сценарий — отдельный класс/функция в application с одним публичным методом | TODO(template): путь |
| DTO на границах | вход/выход use case и presentation; доменные сущности и ORM-модели не пересекают границу процесса (HTTP, очередь) | TODO(template): путь |
| Dependency Injection | все зависимости — через конструктор/параметры; сборка только в composition root | TODO(template): DI-контейнер или ручная сборка, путь |
| TODO(template): Unit of Work / Outbox / Specification — если применяются | когда именно | путь |

Пример (замените своим): `CreateOrderUseCase` в
`src/application/orders/create_order.py` получает `OrderRepository` (интерфейс из
`src/domain/orders/repository.py`) через конструктор; реализация
`PgOrderRepository` лежит в `src/infrastructure/db/repositories/`.

## Анти-паттерны (запрещено)

| Анти-паттерн | Почему запрещён |
|---|---|
| Бизнес-логика в контроллерах / handlers | недоступна для переиспользования и unit-тестов; presentation только транслирует вызовы в application |
| Импорт infrastructure из domain / application | ломает правило зависимостей; ловится арх-линтером ([overview.md](overview.md)) |
| ORM-модели или сущности БД в роли доменных сущностей «наружу» | схема БД начинает диктовать домен и API; смена хранилища становится невозможной |
| Service Locator, глобальные синглтоны вместо DI | скрытые зависимости, нечего подменить в тестах |
| Обход application: presentation ходит в репозиторий напрямую | сценарий размазывается, права и транзакции теряются |
| «God object», сервисы `*Manager` / `*Helper` со смешанными обязанностями | нарушает SRP; имена должны идти из глоссария (см. [code-style.md](code-style.md)) |
| TODO(template): специфичные для проекта запреты | причина |

## Как пользоваться

- Пишешь новый код — сначала проверь таблицу: для задачи есть принятый паттерн?
  Используй его, не изобретай параллельный.
- Хочешь отступить от паттерна или ввести новый — не молча: черновик ADR
  ([../decisions/0000-template.md](../decisions/0000-template.md)) и обсуждение.
- Нашёл анти-паттерн в коде вне своей задачи — зафиксируй в
  [../current/progress.md](../current/progress.md), не чини мимоходом.
