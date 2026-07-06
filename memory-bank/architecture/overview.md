---
status: draft
version: "1.0"
updated: 2026-07-05
verified_commit: ""
owner: ""
---

# Архитектура {{PROJECT_NAME}}: обзор

Канонический документ о слоях и границах. Почему выбрана Clean Architecture —
ADR [0001-clean-architecture](../decisions/0001-clean-architecture.md).
Стек — [tech-stack.md](tech-stack.md), паттерны внутри слоёв — [patterns.md](patterns.md).

## Слои

Базовый набор — четыре слоя. TODO(template): адаптируйте под проект (объединить
application и domain, выделить слой API-контрактов и т.п.) и зафиксируйте итоговый
вариант здесь и в ADR.

| Слой | Что содержит | Что запрещено | Каталог в коде |
|---|---|---|---|
| domain | сущности, value objects, доменные сервисы, интерфейсы репозиториев, доменные ошибки | любые импорты из внешних слоёв; фреймворки, ORM, HTTP, I/O | TODO(template): напр. `src/domain/` |
| application | use cases / interactors, порты (интерфейсы к инфраструктуре), DTO границ, оркестрация транзакций | знание о конкретной БД/HTTP/UI; бизнес-правила, дублирующие domain | TODO(template): напр. `src/application/` |
| infrastructure | реализации репозиториев и портов, ORM-модели, клиенты внешних API, брокеры, кеши | бизнес-логика; обращения к presentation | TODO(template): напр. `src/infrastructure/` |
| presentation | контроллеры/handlers, роутинг, сериализация запросов/ответов, middleware | бизнес-логика; доступ к БД в обход application | TODO(template): напр. `src/presentation/` |

## Правило зависимостей

Зависимости направлены **только внутрь**: presentation → application → domain.
Infrastructure зависит от application/domain (реализует их интерфейсы), но не наоборот.

- domain не знает ни о ком.
- application знает только о domain.
- infrastructure и presentation знают об application и domain, но не друг о друге
  напрямую — связываются через composition root.
- Пересечение границы — только через интерфейс (порт) и DTO, см. [patterns.md](patterns.md).

## Composition root

Единственное место, где слои «склеиваются» (создание зависимостей, конфигурация DI):
TODO(template): путь, напр. `src/main.py` / `cmd/server/main.go`.

## Как проверяется соблюдение границ

TODO(template): укажите инструмент, путь к конфигу и CI-джобу. Проверка границ
обязательна в CI — «глазами на ревью» не считается.

Варианты по стеку: import-linter (Python), ArchUnit (Java/Kotlin),
dependency-cruiser (TS/JS), NetArchTest (.NET), go-arch-lint (Go).

Пример (замените своим): Python + import-linter; конфиг `.importlinter` в корне,
контракт `layers: presentation -> application -> domain`; запускается командой
`lint-imports` в CI-джобе `lint` и в pre-commit.

## Чек-лист при изменении структуры

- [ ] Новый каталог/модуль однозначно отнесён к слою из таблицы выше.
- [ ] Не появился импорт «наружу» — подтверждено арх-линтером.
- [ ] Изменение самих границ оформлено как ADR ([../decisions/README.md](../decisions/README.md))
      и отражено в этом документе.
- [ ] Таблица каталогов актуальна.
