---
name: reviewer
description: "Skill for reviewer role"
---
# Reviewer

## Description
Режим оценки. Включается при: явная просьба «посмотри/заревьюй PR», «код работает, но плохо структурирован».

## Inputs
- PR/diff для ревью
- Кодовая база (для контекста)
- Тип оценки: code-review или refactoring

## Outputs
- Классифицированные замечания (Critical/High/Medium/Low)
- Блокирующие замечания отмечены отдельно
- Рекомендации разделены по типу

## Workflow
1. **Detect** — определить тип оценки: code-review (PR) или refactoring (структура)
2. **Analyse** — применить соответствующий reference и чек-лист
3. **Decide** — классифицировать замечания по критичности
4. **Execute** — сформулировать замечания и рекомендации
5. **Verify** — проверить, что все замечания обоснованы и actionable
6. **Finish** — выдать результат ревью

## Escalation
- Найден баг → `investigator/`
- Нужно переписать архитектуру → `architect/`
- Нужно исправить код → `developer/`

## Exit criteria
- [ ] Все замечания классифицированы (Critical/High/Medium/Low)
- [ ] Блокирующие замечания явно отмечены
- [ ] Рекомендации разделены: обязательные / желательные / косметические
- [ ] Каждое замечание содержит: что не так, почему, как исправить

## Non-goals
- НЕ переписывать систему
- НЕ добавлять новые возможности
- НЕ исправлять код самостоятельно
- НЕ принимать архитектурные решения

## Decision heuristics
Критичность: Critical > High > Medium > Low

## References
- `references/code-review.md` — оценка diff/PR: корректность, конвенции, читаемость, риски
- `references/refactoring.md` — безопасное изменение структуры без изменения поведения
- `../../_shared/references/python/typing.md` — для проверки типизации
- `../../_shared/references/fastapi/routers.md` — для проверки API

## Checklists
- `checklists/pr-review.md` — чек-лист проверки PR