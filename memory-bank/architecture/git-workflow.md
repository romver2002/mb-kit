---
status: draft
version: "1.0"
updated: 2026-07-05
verified_commit: ""
owner: ""
---

# Git workflow

Ветки, коммиты и PR в {{PROJECT_NAME}}. Ограничения для агентов (не коммитить
без явной просьбы) — [AGENTS.md](../../AGENTS.md); процессы под тип задачи —
[../workflows/new-feature.md](../workflows/new-feature.md) и соседние файлы.

## Модель веток

TODO(template): зафиксируйте модель (trunk-based / GitHub Flow / git-flow) и уберите лишнее.

Пример (замените своим): trunk-based — `main` всегда релизопригоден; работа
в короткоживущих ветках (< 3 дней) от `main`; слияние только через PR.
Как из `main` получается релиз — [../ops/releases.md](../ops/releases.md).

## Именование веток

Формат: `<тип>/<задача>-<краткое-описание>`;
типы: `feature` | `fix` | `refactor` | `hotfix` | `chore`.

Пример (замените своим): `feature/PROJ-142-order-cancellation`,
`fix/PROJ-155-tz-in-reports`. TODO(template): формат номера задачи под ваш трекер.

## Коммиты

- Формат сообщения: TODO(template): подтвердите Conventional Commits
  (`тип(область): описание`) или зафиксируйте свой; перечислите допустимые области.
- Пример (замените своим): `feat(orders): add cancellation deadline check (PROJ-142)`.
- Коммит атомарен: одна логическая правка; проект собирается, тесты проходят.
- **Авторство — только человек.** В сообщениях коммитов запрещены AI-трейлеры
  и любая атрибуция инструментов: `Co-Authored-By: Claude ...`,
  `Generated with ...` и аналогичные. Автор и коммиттер — человек, ведущий работу.

## Pull Request

- Размер: ориентир — до ~400 изменённых строк; больше — делите на цепочку PR.
- Описание PR: что и зачем; ссылка на задачу и ADR (если решение оформлялось);
  как проверялось. Правки `memory-bank/` упоминаются отдельно
  ([governance](../_meta/governance.md)).
- Обязательные проверки (все зелёные в CI до мержа):
  - [ ] форматтер, линтер, типы, арх-линтер — [code-style.md](code-style.md),
        [overview.md](overview.md);
  - [ ] тесты по [testing-strategy.md](testing-strategy.md);
  - [ ] валидатор базы знаний, если задет `memory-bank/`
        (`python scripts/validate_memory_bank.py`);
  - [ ] TODO(template): остальные обязательные джобы CI.
- Ревью: минимум TODO(template): N апрувов; для изменений с внешним вводом,
  auth или ПДн — дополнительно чек-лист из [security.md](security.md);
  правки `memory-bank/` (кроме `current/`) — апрув владельца раздела.

## Кто и как мержит

- Мержит автор после апрувов и зелёного CI.
  TODO(template): подтвердить или заменить (мержит ревьюер / merge queue).
- Способ слияния: TODO(template): squash / merge commit / rebase — выберите один
  и включите его единственным в настройках репозитория.
- Прямые пуши в `main` запрещены (branch protection).
- Срочные исправления в проде — по процессу [../workflows/hotfix.md](../workflows/hotfix.md).
