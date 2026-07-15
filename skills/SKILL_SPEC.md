# Skill Specification

## Структура
Каждый Skill — это директория:
```
skills/<role>/
├── SKILL.md
├── references/  # специфичные для роли знания
└── checklists/  # процедурные материалы
```

Общие знания (postgres, fastapi, python, docker, mcp) находятся в `skills/references/`.

## Обязательные разделы SKILL.md
1. Name — короткое имя роли
2. Description — что делает, когда включать
3. Inputs — что получает на вход
4. Outputs — что выдаёт на выходе
5. Workflow — пошаговый процесс (рекомендуется DAD: Detect-Analyse-Decide-Execute-Verify-Finish)
6. Escalation — когда передать управление другому Skill
7. Exit criteria — чек-лист завершения работы
8. Non-goals — что Skill НЕ делает
9. Decision heuristics — приоритеты при принятии решений (опционально, но рекомендовано)
10. References — список файлов с описанием, когда какой использовать
11. Checklists — список процедурных материалов

## Стиль
- Императивный, без воды
- Каждое предложение — действие или критерий
- Настолько кратко, насколько возможно, но не в ущерб ясности
- Не писать "под размер" — писать по сути

## Ссылки на общие знания
Использовать относительные пути:
- `../../references/postgres/schema.md` — из `skills/architect/` в `skills/references/postgres/`
- `../references/planning.md` — из `skills/architect/checklists/` в `skills/architect/references/`

## Не делать
- Не дублировать знания из общих references/
- Не писать общие знания про Python/FastAPI — они уже в `skills/references/`
- Не создавать универсальные Skills