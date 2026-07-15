# Python — Packaging (uv/poetry)

## uv (рекомендуется)
```bash
# Инициализация
uv init my-project
cd my-project

# Добавление зависимостей
uv add fastapi uvicorn
uv add --dev pytest ruff mypy

# Запуск
uv run python main.py
uv run pytest
uv run ruff check .
```

## pyproject.toml
```toml
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.115.0",
    "uvicorn[standard]>=0.30.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "ruff>=0.5.0",
    "mypy>=1.10",
]

[tool.ruff]
line-length = 100
target-version = "py312"

[tool.pytest.ini_options]
asyncio_mode = "auto"
```

## Poetry (альтернатива)
```bash
poetry new my-project
poetry add fastapi uvicorn
poetry add --group dev pytest ruff mypy
poetry run pytest
```

## Lock-файлы
- `uv.lock` / `poetry.lock` — всегда коммитить в репозиторий
- Обеспечивает детерминированные сборки
- Обновлять осознанно: `uv lock --upgrade-package fastapi`

## Версионирование
- Следуй SemVer: `MAJOR.MINOR.PATCH`
- `>=1.0,<2.0` — совместимые версии
- `^1.2.3` (poetry) — >=1.2.3, <2.0.0
- Закрепляй dev-зависимости менее строго

## Антипаттерны
- `*` в версиях зависимостей
- Отсутствие lock-файла в репозитории
- Глобальная установка пакетов вместо venv