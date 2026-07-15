# Docker — Dockerfile Best Practices

## Многоэтапная сборка
```dockerfile
# Stage 1: Build
FROM python:3.12-slim AS builder
WORKDIR /app
COPY pyproject.toml uv.lock ./
RUN pip install uv && uv sync --frozen --no-dev
COPY . .

# Stage 2: Runtime
FROM python:3.12-slim
WORKDIR /app
COPY --from=builder /app/.venv .venv
COPY --from=builder /app/src src
ENV PATH="/app/.venv/bin:$PATH"
USER nobody
CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## Best Practices
- Используй конкретные теги образов (`3.12-slim`, не `latest`)
- Минимизируй слои: объединяй RUN-команды
- `.dockerignore`: исключи `.git`, `__pycache__`, `.venv`, `.env`
- Не запускай от root: `USER nobody`
- HEALTHCHECK для оркестрации
- Один процесс на контейнер

## Порядок слоёв
1. Базовый образ (меняется редко)
2. Системные зависимости (apt, системные пакеты)
3. Зависимости приложения (pip/uv — меняется при изменении зависимостей)
4. Код приложения (меняется чаще всего)

## Переменные окружения
```dockerfile
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1
```

## Антипаттерны
- `latest` тег в продакшене
- Секреты в ENV или ARG (попадают в историю слоёв)
- `pip install` без `--no-cache-dir`
- Запуск нескольких процессов в одном контейнере