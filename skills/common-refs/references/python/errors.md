# Python — Error Handling

## Базовые исключения
```python
class AppError(Exception):
    """Базовое исключение приложения."""
    def __init__(self, message: str, code: str | None = None):
        self.message = message
        self.code = code
        super().__init__(message)

class NotFoundError(AppError):
    ...

class ValidationError(AppError):
    ...

class ConflictError(AppError):
    ...
```

## Обработка в FastAPI
```python
from fastapi import HTTPException

@router.get("/{user_id}")
async def get_user(user_id: UUID):
    user = await repo.get(user_id)
    if not user:
        raise HTTPException(status_code=404, detail="User not found")
    return user
```

## Глобальный обработчик
```python
from fastapi import Request
from fastapi.responses import JSONResponse

@app.exception_handler(AppError)
async def app_error_handler(request: Request, exc: AppError):
    return JSONResponse(
        status_code=400,
        content={"error": exc.message, "code": exc.code},
    )
```

## Try/Except
```python
try:
    result = await external_call()
except ConnectionError:
    logger.error("External service unavailable")
    raise AppError("Service temporarily unavailable")
except Exception:
    logger.exception("Unexpected error")
    raise
```

## Принципы
- Лови конкретные исключения, не `except Exception` без необходимости
- Всегда логируй с контекстом (что делали, с какими параметрами)
- Не проглатывай исключения молча
- Оборачивай внешние исключения в доменные
- Сообщения об ошибках для пользователя ≠ сообщения для логов

## Антипаттерны
- `except: pass` — никогда
- `except Exception: pass` — почти никогда
- Возврат `None` вместо исключения
- Строковые исключения (`raise "error"`)