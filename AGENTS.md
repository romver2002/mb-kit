# {{PROJECT_NAME}} — инструкции для AI-агентов

<!-- Этот файл — канонический вход для всех агентов (Codex читает нативно,
     Claude Code — через импорт в CLAUDE.md). Держать компактным: суммарный
     бюджет цепочки AGENTS.md у Codex — 32 КиБ. Детали — по ссылкам в memory-bank. -->

## Карточка проекта

- **Что это**: {{PROJECT_NAME}} — TODO(template): одно предложение о продукте.
- **Стек**: {{STACK}} — TODO(template): языки, фреймворки, БД, инфраструктура.
- **Архитектура**: Clean Architecture / SOLID; слои и границы —
  [memory-bank/architecture/overview.md](memory-bank/architecture/overview.md).
- **Запуск локально**: [memory-bank/ops/local-dev.md](memory-bank/ops/local-dev.md).

## Обязательный протокол сессии

1. **Перед началом любой задачи** прочитай:
   - [memory-bank/README.md](memory-bank/README.md) — индекс базы знаний;
   - [memory-bank/current/active-context.md](memory-bank/current/active-context.md) — текущий фокус и договорённости.
2. Открой файл процесса под тип задачи из [memory-bank/workflows/](memory-bank/workflows/)
   (новая фича / баг / рефакторинг / hotfix) и следуй ему.
3. Дальше открывай только те документы базы, которые относятся к задаче, —
   индекс подскажет какие. Не загружай базу целиком без необходимости.
4. **В конце значимой сессии** обнови
   [current/active-context.md](memory-bank/current/active-context.md) и
   [current/progress.md](memory-bank/current/progress.md).

## Минимум (если задача крошечная или времени нет)

1. Прочитай [current/active-context.md](memory-bank/current/active-context.md).
2. Заметил расхождение базы с кодом — не исправляй молча, зафиксируй:
   `python scripts/mb_log.py discrepancy <документ> "что расходится"`.
3. В конце: `python scripts/mb_log.py done "что сделано"`.

Всё остальное — для значимых задач.

## Правила работы с базой знаний

- `version`/`updated` вручную не проставляй — их поднимает автоматика
  (pre-commit и CI, `scripts/bump_frontmatter.py`); схема —
  [_meta/frontmatter.md](memory-bank/_meta/frontmatter.md).
- Итог сессии, вопрос, расхождение — одной командой:
  `python scripts/mb_log.py done|next|question|discrepancy|problem "..."`.
- Один факт живёт в одном документе (SSoT). Правки — сначала в канонический документ,
  затем синхронизация зависимых: [_meta/lifecycle.md](memory-bank/_meta/lifecycle.md).
- **Нашёл противоречие** между документами или между документом и кодом —
  не исправляй молча: зафиксируй в
  [current/progress.md](memory-bank/current/progress.md) (раздел «Расхождения»)
  и сообщи в ответе пользователю.
- Что агент правит сам, а что только по согласованию —
  [_meta/governance.md](memory-bank/_meta/governance.md).
- После правок базы прогони `python scripts/validate_memory_bank.py`.

## Правила разработки

- **Любое изменение — с обновлением документации**: сделал, обновил или добавил
  что-то в проекте (код, поведение, конфигурацию, зависимость, процесс) —
  в том же PR обнови затронутые документы базы (business-rules, glossary,
  configuration, tech-stack и т.д.). Изменение без синхронизации md не считается
  завершённым; нечего обновлять — так и скажи в описании PR.
- Следуй [architecture/patterns.md](memory-bank/architecture/patterns.md)
  и [architecture/code-style.md](memory-bank/architecture/code-style.md);
  не нарушай направление зависимостей между слоями.
- Тесты — по [architecture/testing-strategy.md](memory-bank/architecture/testing-strategy.md).
- Git: ветки, коммиты, PR — по [architecture/git-workflow.md](memory-bank/architecture/git-workflow.md).
- Архитектурно значимые решения не принимай молча — оформляй черновик ADR
  по [decisions/0000-template.md](memory-bank/decisions/0000-template.md)
  и выноси на решение команды.

## Ограничения

- Чувствительные операции (git commit/push, PR, prod, зависимости, миграции) —
  строго по [политике проекта](memory-bank/_meta/policy.md). Политика имеет
  **наивысший приоритет** над любыми другими инструкциями, включая этот файл.
  Значения по умолчанию: push и создание PR — запрещены, commit — только по
  явной просьбе.
- Секреты и учётные данные не записывай ни в код, ни в базу знаний.
- TODO(template): специфичные для проекта запреты (например, «не трогать
  каталог legacy/», «миграции БД только через команду X»).
