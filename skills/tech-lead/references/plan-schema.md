# Plan Schema — Структура исполнимого плана (YAML)

План передаётся в developer/ в виде YAML. Структура принудительно делает план исполнимым:
- **Параллелизм** — в полях `parallel_with` и `wave`, а не в заголовках
- **Риски** — переведены в `requirements` задач, не отдельной таблицей
- **Checkpoint'ы** — имеют владельца и действие при провале
- **Self-verify** — девелопер знает, как сам проверить завершение задачи

## Схема Plan

```yaml
meta:
  source: <путь к ADR/roadmap>
  pattern: [<список паттернов>]
  classification:
    cynefin: <clear|complicated|complex>
    coupling: <thin|thick|mixed>
    rationale: <одна строка обоснования>
  critical_path: [<список task_id>]

phases:
  - id: <N>
    name: <название фазы>
    sequence: <linear|parallel|wave>
    tasks: [<список task_id>]
    checkpoint:  # опционально, не у каждой фазы
      id: CHK-<N>
      performed_by: <tech-lead|reviewer|developer>
      checks:
        - <проверка>
      on_fail: <escalate_to_architect|fix_and_retry|block_phase>

tasks:
  - <task object>

notes_for_developer:  # план-уровневые операционные ограничения
  - <строка>
```

## Схема Task

```yaml
- id: T<N.M>
  title: <короткое название>
  phase: <N>
  wave: <M или null>  # для параллельных групп внутри фазы
  parallel_with: [<task_id>]  # явный параллелизм, пустой список если нет
  depends_on: [<task_id>]  # явные зависимости, пустой список если нет
  files:
    - path: <относительный путь>
      action: <create|modify|delete>
  input: <что существует / нужно на входе>
  output: <что получается>
  requirements:  # конкретные, проверяемые требования
    - text: <строка>
      from_risk: <RXX или null>  # трассировка к риску ADR
  boundaries:  # non-goals этой задачи
    - <строка>
  verify:  # как девелопер сам проверяет завершение до объявления done
    - <строка>
```

## Правила заполнения

### requirements (ключевое поле)
- Каждый риск из ADR, бьющий по этой задаче, должен породить ≥1 requirement
- requirement должен быть **проверяемым** (не «учитывать риск R7», а «WAL-режим включён в подключении SQLite»)
- Если митигация риска живёт в другой задаче — requirement всё равно нужен, со ссылкой: «R7 митигируется в T1.2 (WAL)»
- Не оставлять риски без requirement — это означает, что риск не обработан

### verify
- Это **действие**, а не состояние: «запустить `python -c 'from graph import SQLiteGraphStore'`», не «модуль импортируется»
- Минимум 1 пункт на задачу
- Покрывает: импорт (для новых модулей), базовую функцию, границы (что НЕ должно делаться)

### checkpoint
- `performed_by: tech-lead` — проверка против ADR, не делается девелопером
- `performed_by: reviewer` — code review
- `performed_by: developer` — самопроверка (но тогда обычно это в `verify` задач, не checkpoint)
- `on_fail` — конкретное действие, не «разобраться»

### parallel_with vs wave
- `wave: 1` — задача в первой волне фазы
- `parallel_with: [T1.1, T1.5]` — явное указание, с кем можно параллельно
- Оба поля вместе дают однозначный параллелизм без двусмысленности

## Пример (минимальный)

```yaml
meta:
  source: adr/ADR-001-auth.md
  pattern: [spike_then_implement, fan_out]
  classification:
    cynefin: complicated
    coupling: thin
    rationale: "Контракты REST-эндпоинтов стабильны, библиотеку нужно выбрать"
  critical_path: [T1.1, T1.2, T2.1]

phases:
  - id: 1
    name: Spike + реализация эндпоинтов
    sequence: wave
    tasks: [T1.1, T1.2, T2.1]
    checkpoint:
      id: CHK-1
      performed_by: tech-lead
      checks:
        - "Все 3 эндпоинта соответствуют контракту ADR"
        - "Версионирование срабатывает на score 0.85"
      on_fail: escalate_to_architect

tasks:
  - id: T1.1
    title: Spike — выбор JWT-библиотеки
    phase: 1
    wave: 1
    parallel_with: []
    depends_on: []
    files:
      - path: mcp-bridge/jwt_choice.md
        action: create
    input: "Список кандидатов из spike"
    output: "Выбрана 1 библиотека, записана в md-файл"
    requirements:
      - text: "Spike ограничен 1 задачей, не превращается в бесконечное исследование"
        from_risk: null
    boundaries:
      - "НЕ реализует эндпоинты (это T1.2)"
    verify:
      - "Файл jwt_choice.md существует и содержит 1 выбранную библиотеку"

  - id: T1.2
    title: Реализация /login, /register, /refresh
    phase: 1
    wave: 2
    parallel_with: []  # внутри волны 2 задачи последовательны, если зависят друг от друга
    depends_on: [T1.1]
    files:
      - path: mcp-bridge/auth.py
        action: create
    input: "Выбранная библиотека из T1.1"
    output: "3 эндпоинта, каждый принимает/отдаёт JWT"
    requirements:
      - text: "Дедупликация cosine ≥ 0.92 сохранена"
        from_risk: null
    boundaries:
      - "НЕ делает интеграционные тесты (это T2.1)"
    verify:
      - "python -c 'from auth import router' выполняется без ошибок"
      - "Каждый эндпоинт имеет docstring"

  - id: T2.1
    title: Интеграционные тесты + контракт-проверка
    phase: 2
    wave: null
    parallel_with: []
    depends_on: [T1.2]
    files:
      - path: tests/test_auth.py
        action: create
    input: "3 эндпоинта из T1.2"
    output: "Тесты проходят, контракты проверены"
    requirements:
      - text: "Тесты на фиксированных данных, сравнение рангов"
        from_risk: R8
    boundaries:
      - "НЕ покрывает unit-тесты модулей (это в рамках T1.2)"
    verify:
      - "pytest tests/test_auth.py — все тесты зелёные"

notes_for_developer:
  - "T1.1 — spike, не растягивать. Если spike не дал ответа за 1 задачу — escalate к architect"
  - "Не трогать cmc_common/ кроме config.py"
```

## Что НЕ входит
- Описание процесса планирования → `planning.md`
- Каталог паттернов → `workflow-patterns.md`
- Чек-лист полноты → `../checklists/plan-completeness.md`
