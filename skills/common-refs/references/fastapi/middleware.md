# FastAPI — Middleware

## Встроенные
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_methods=["*"],
    allow_headers=["*"],
)
```

## Пользовательские
```python
from starlette.middleware.base import BaseHTTPMiddleware

class TimingMiddleware(BaseHTTPMiddleware):
    async def dispatch(self, request, call_next):
        start = time.time()
        response = await call_next(request)
        duration = time.time() - start
        response.headers["X-Process-Time"] = str(duration)
        return response
```

## Порядок middleware
- Первый добавленный — самый внешний (выполняется первым на вход, последним на выходе)
- Типовой порядок: CORS → Логирование → Аутентификация → Rate Limiting → Бизнес-логика

## Background Tasks
```python
from fastapi import BackgroundTasks

@router.post("/")
async def create_user(
    body: UserCreate,
    background_tasks: BackgroundTasks,
):
    user = await create_user_in_db(body)
    background_tasks.add_task(send_welcome_email, user.email)
    return user
```

## Когда использовать
- Отправка email/уведомлений
- Запись в лог/аналитику
- Инвалидация кэша
- НЕ для критичных операций (нет гарантии выполнения)

## Антипаттерны
- Тяжёлая логика в middleware (замедляет каждый запрос)
- Изменение тела запроса/ответа без необходимости
- Background tasks для операций, требующих транзакционности