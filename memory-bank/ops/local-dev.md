---
status: draft
version: "1.1"
updated: 2026-07-07
verified_commit: ""
owner: ""
---

# Локальный запуск {{PROJECT_NAME}}

Цель: разработчик или агент поднимает проект с нуля на чистой машине,
не задавая вопросов команде. Переменные окружения — в
[configuration.md](configuration.md), версии стека — в
[../architecture/tech-stack.md](../architecture/tech-stack.md).

## Предусловия

TODO(template): перечислить инструменты с точными версиями и способом установки.

- [ ] TODO(template): рантайм/SDK ({{STACK}}) — версия, откуда ставить
- [ ] TODO(template): менеджер зависимостей — версия
- [ ] TODO(template): Docker / docker compose — если нужны для инфраструктуры
- [ ] TODO(template): доступы (git-репозиторий, приватный registry, VPN)

Пример (замените своим): .NET SDK 8.0.x (`winget install Microsoft.DotNet.SDK.8`),
Docker Desktop ≥ 4.30, доступ к GitHub-организации.

## Первичная настройка (однократно на клон)

Сразу после клона включи предохранители репозитория одной командой:

```bash
python scripts/setup.py
```

Она включает git pre-commit-хук (`core.hooksPath`), который автоматически поднимает
`version`/`updated` документов базы при коммите — вручную помнить о версионировании не
нужно. Проверить состояние: `python scripts/setup.py --check`. Механизм версий —
[../_meta/frontmatter.md](../_meta/frontmatter.md). Хук — только локальное удобство;
непробиваемый рубеж всё равно даёт проверка в CI.

## Шаги запуска с нуля

1. Клонировать репозиторий и перейти в корень.
2. Создать локальную конфигурацию: TODO(template): например, скопировать
   `.env.example` → `.env` и заполнить значения по [configuration.md](configuration.md).
3. Поднять инфраструктуру: TODO(template): команда (например, `docker compose up -d`)
   и список сервисов (БД, кэш, брокер).
4. Установить зависимости: TODO(template): команда.
5. Применить миграции БД / сиды: TODO(template): команда; откуда берутся тестовые данные.
6. Запустить приложение: TODO(template): команда запуска и порт по умолчанию.

Пример (замените своим):

```bash
git clone git@github.com:org/{{PROJECT_NAME}}.git && cd {{PROJECT_NAME}}
cp .env.example .env
docker compose up -d postgres redis
dotnet restore && dotnet ef database update
dotnet run --project src/Api        # http://localhost:5000
```

## Как проверить, что всё поднялось

- [ ] TODO(template): healthcheck-эндпоинт (например, `GET /health` → 200)
- [ ] TODO(template): один сквозной сценарий (логин / главная страница / ключевой запрос)
- [ ] Тесты проходят локально: TODO(template): команда
  (какие уровни гонять — [../architecture/testing-strategy.md](../architecture/testing-strategy.md))
- [ ] В логах при старте нет ERROR (куда смотреть — [observability.md](observability.md))

## Типовые проблемы запуска

Дополняй таблицу при каждой новой проблеме, отнявшей > 15 минут.

| Симптом | Причина | Решение |
|---|---|---|
| Пример (замените своим): `connection refused` к БД при старте | Контейнер БД ещё не готов, приложение стартовало раньше | Дождаться healthy: `docker compose ps`; перезапустить приложение |
| TODO(template): симптом | причина | решение |

## Вопросы заполняющему

- Что нужно от «голой» ОС до первого успешного запуска? Пройди сам и запиши.
- Есть ли шаги, различающиеся по ОС (Windows/macOS/Linux)? Отметь явно.
- Что из инфраструктуры можно замокать локально, а что обязательно живое?
