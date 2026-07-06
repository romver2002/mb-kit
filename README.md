# Шаблон базы знаний проекта (memory bank) для AI-driven разработки

Production-шаблон базы знаний, которую перечитывают AI-агенты (Claude Code, Codex и
любые инструменты, поддерживающие стандарт [AGENTS.md](https://agents.md/)) и люди.
Раскатывается копированием в корень каждого проекта группы и адаптируется под него.

Версия шаблона: см. [CHANGELOG.md](CHANGELOG.md).

## Зачем

У агентов два слоя памяти: собственная (машинно-локальная, не versioned, «сохранил и забыл»)
и файлы репозитория, которые перечитываются при старте каждой сессии. Этот шаблон переносит
знания во второй слой: git-версионируемый, ревьюемый в PR, общий для команды и всех агентов.

## Состав

| Путь | Назначение |
|---|---|
| [AGENTS.md](AGENTS.md) | Канонический вход для агентов: карточка проекта + обязательный протокол. Читается Codex и 20+ инструментами нативно |
| [CLAUDE.md](CLAUDE.md) | Мост для Claude Code: импортирует `@AGENTS.md` + специфика Claude |
| [.claude/rules/](.claude/rules/) | Модульные правила Claude Code; правила с `paths:` подгружаются только при работе с соответствующими файлами |
| [.claude/settings.json](.claude/settings.json) | hooks Claude Code: напоминание протокола при старте сессии |
| [memory-bank/](memory-bank/README.md) | Сама база знаний. Вход — `memory-bank/README.md` (роутер) |
| [memory-bank/_meta/](memory-bank/_meta/governance.md) | Управляющий слой: политика прав агентов (policy.md, наивысший приоритет), схема frontmatter, жизненный цикл, зоны ответственности |
| [scripts/validate_memory_bank.py](scripts/validate_memory_bank.py) | Валидатор: реестр правил MB001–MB081 (frontmatter, ссылки, сироты, свежесть, политика); `--list-rules`, `--json`, `--strict`, юнит-тесты |
| [scripts/bump_frontmatter.py](scripts/bump_frontmatter.py) | Авто-bump `version`/`updated`: pre-commit ([git-hooks/](scripts/git-hooks/pre-commit)) чинит сам, CI — рубеж |
| [scripts/mb_log.py](scripts/mb_log.py) | Запись в `current/` одной командой: `done`/`next`/`question`/`discrepancy`/`problem`/`debt` |
| [scripts/check_session_close.py](scripts/check_session_close.py) | Stop-hook: сессия, менявшая код, не закрывается без записи в `current/` |
| [scripts/kb_log_read.py](scripts/kb_log_read.py) + [kb_usage_report.py](scripts/kb_usage_report.py) | Локальная телеметрия чтения базы: доля сессий с базой, мёртвые файлы (лог гитигнорится) |
| [.github/workflows/validate-memory-bank.yml](.github/workflows/validate-memory-bank.yml) | CI: валидатор гоняется на каждом PR, задевающем базу |

## Раскатка на проект

Раскатка двухуровневая. Ядро (копируется сразу): `AGENTS.md`, `CLAUDE.md`, `.claude/`,
`memory-bank/README.md`, `_meta/`, `current/`, `domain/glossary.md`,
`architecture/overview.md`, `ops/local-dev.md`, `decisions/`. Остальные файлы
добавляйте по мере появления первого реального факта — пустая заготовка хуже
отсутствия файла (валидатор считает достижимость от индекса, поэтому
неиспользуемые файлы просто уберите из `memory-bank/README.md` и с диска).

1. Скопируйте в корень проекта: `AGENTS.md`, `CLAUDE.md`, `.claude/`, `.gitignore`
   (или добавьте его строки в существующий), `memory-bank/`, `scripts/`,
   `.github/workflows/validate-memory-bank.yml`.
2. Замените плейсхолдеры `{{PROJECT_NAME}}`, `{{STACK}}` и т.п. в `AGENTS.md`.
3. Включите авто-bump версий: `git config core.hooksPath scripts/git-hooks`
   (на unix ещё `chmod +x scripts/git-hooks/pre-commit`).
4. Прогоните валидатор: `python scripts/validate_memory_bank.py`.
5. Проверьте на своей версии Claude Code, что path-scoped правила `.claude/rules`
   реально подгружаются (тестовой правкой файла под глоб).
6. Дайте агенту промпты первичного наполнения (ниже) — по одному, с ревью результата.
7. Все оставшиеся маркеры `TODO(template)` — это места, требующие адаптации;
   валидатор их подсчитывает.

### Промпты первичного наполнения (Claude Code / Codex)

Выполнять по очереди, результат каждого — ревьюить как обычный PR:

```text
Изучи кодовую базу проекта и заполни memory-bank/architecture/: overview.md
(слои Clean Architecture, границы, поток зависимостей), tech-stack.md, patterns.md.
Соблюдай схему frontmatter из memory-bank/_meta/frontmatter.md. Не выдумывай:
что не удалось установить по коду — оставь TODO(template) с вопросом к команде.
```

```text
Заполни memory-bank/domain/ по кодовой базе: glossary.md (термины предметной
области из имён сущностей/модулей), domain-model.md, bounded-contexts.md.
Спорные трактовки помечай "требует подтверждения команды".
```

```text
Заполни memory-bank/ops/local-dev.md и configuration.md по README, docker-compose,
CI-конфигам и env-примерам проекта. Ничего не запускай без подтверждения.
```

`product/` и `business-rules.md` агент по коду достоверно не восстановит —
их заполняет команда (тимлид/владелец продукта) руками или в диалоге с агентом.

## Поддержание

- Изменения базы идут **через PR** и ревьюятся как код.
- Каждая содержательная правка документа обновляет его frontmatter
  (`version`, `updated`) — регламент в [_meta/lifecycle.md](memory-bank/_meta/lifecycle.md).
- Живые файлы `current/` агент обновляет в конце значимых сессий сам;
  остальные разделы — по правилам зон ответственности
  из [_meta/governance.md](memory-bank/_meta/governance.md).
- Раз в спринт — регламент обслуживания в [_meta/lifecycle.md](memory-bank/_meta/lifecycle.md):
  перенести устоявшееся из `current/` в постоянные разделы, отработанное удалить.
- Личную автопамять агентов (`/memory` в Claude Code) считать черновиком:
  полезное периодически переносить сюда.

## Интеграция со смежными инструментами

**spec-kit** (GitHub, Spec-Driven Development). Совместим и рекомендован для крупных фич:
`/speckit.specify → plan → tasks → implement` порождает одноразовые артефакты в
`specs/NNN-фича/` — это рабочие наряды, в memory-bank их не переносить. Соответствие:
`.specify/memory/constitution.md` ≈ наш `AGENTS.md` + `memory-bank/_meta/` (при внедрении
spec-kit constitution должен ссылаться на memory-bank, а не дублировать его).
После мерджа фичи: решения → `decisions/`, изменения архитектуры → `architecture/`,
статус → `current/progress.md`.

**ruflo (ex-claude-flow)**. Опциональный слой оркестрации мультиагентных сценариев.
Его память (`.swarm/memory.db`, SQLite) — служебный координационный кэш агентов:
бинарный, неревьюемый, невидимый для Codex. Источником истины остаётся markdown
в `memory-bank/`; в `.gitignore` проекта добавьте `.swarm/`.

## Почему структура именно такая

- **Короткий всегда-загружаемый вход + тематические файлы по запросу**: у Claude Code
  и Codex жёсткий бюджет контекста на автозагрузку (Codex обрезает цепочку AGENTS.md
  на 32 КиБ). Поэтому `AGENTS.md` — компактный протокол со ссылками, а не свалка.
- **Разделение стабильного и живого**: справочные разделы меняются редко и ревьюятся
  строго; `current/` — дёшево и часто. Это защищает базу от главного риска —
  превращения в мусор.
- **Версия и дата в тексте, а не только в git**: агент, читая файл, не видит git log —
  документ без даты выглядит для него так же авторитетно, как свежий.
  Поэтому frontmatter обязателен, а валидатор его проверяет.
