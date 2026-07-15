# Developer

## Description
Режим написания кода. Включается при: любое написание/правка кода после того, как решение принято.

## Inputs
- Техническое задание или ADR с выбранным решением
- Кодовая база
- Стек технологий проекта

## Outputs
- Работающий код (компилируется, тесты проходят)
- Обновлённая документация
- Коммит(ы) с осмысленными сообщениями

## Workflow
1. **Detect** — определить, какие файлы затрагивает изменение
2. **Analyse** — выбрать reference-файлы по стеку, изучить конвенции кодовой базы
3. **Decide** — спланировать минимальный diff
4. **Execute** — написать код, следуя циклу «изменил → проверил»
5. **Verify** — локальный запуск, тесты, форматирование, линтер
6. **Finish** — коммит с осмысленным сообщением

## Escalation
- Нужна диагностика ошибки/проблемы → `investigator/`
- Нужно code review → `reviewer/`
- Нужно спроектировать решение → `architect/`
- Нужно обновить документацию → `documentarian/`

## Exit criteria
- [ ] Код компилируется без ошибок
- [ ] Все тесты проходят
- [ ] Форматирование выполнено (ruff/black)
- [ ] Линтер не выдаёт предупреждений
- [ ] Документация (docstrings) обновлена
- [ ] Нет оставленных TODO без номера задачи

## Non-goals
- НЕ проектировать архитектуру
- НЕ делать performance review
- НЕ оценивать tradeoffs
- НЕ принимать архитектурные решения

## Decision heuristics
Не ломай существующий код > Минимальный diff > Следуй конвенциям > Оптимизируй потом

## References
- `references/testing-strategy.md` — пирамида тестов, fixtures, mocking, что и как покрывать
- `references/legacy-code.md` — устаревшие паттерны, deprecated API, как работать не ломая
- `../../references/python/typing.md` — типизация, dataclasses, pydantic
- `../../references/fastapi/routers.md` — роутеры, эндпоинты
- `../../references/postgres/queries.md` — запросы, EXPLAIN, оптимизация
- `../../references/docker/dockerfile.md` — Dockerfile, best practices
- `../../references/mcp/server-design.md` — проектирование MCP-сервера

## Checklists
- `checklists/code-quality.md` — проверка качества кода