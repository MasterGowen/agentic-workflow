# FastAPI — Routers

## Структура
```python
from fastapi import APIRouter

router = APIRouter(prefix="/users", tags=["users"])

@router.get("/")
async def list_users():
    ...

@router.get("/{user_id}")
async def get_user(user_id: UUID):
    ...

@router.post("/")
async def create_user(body: UserCreate):
    ...
```

## Организация
- Один роутер на доменную сущность (`users.py`, `orders.py`)
- Регистрация в `main.py`: `app.include_router(users.router)`
- Вложенные роутеры: `router.include_router(profile.router, prefix="/{user_id}/profile")`

## Эндпоинты
- `GET /resource` — список (с пагинацией)
- `GET /resource/{id}` — один элемент
- `POST /resource` — создание
- `PUT /resource/{id}` — полное обновление
- `PATCH /resource/{id}` — частичное обновление
- `DELETE /resource/{id}` — удаление

## Пагинация
```python
from fastapi import Query

@router.get("/")
async def list_users(
    offset: int = Query(0, ge=0),
    limit: int = Query(20, ge=1, le=100),
):
    ...
```

## Статус-коды
- `201` — создание
- `204` — удаление (без тела ответа)
- `404` — не найдено (использовать HTTPException)
- `409` — конфликт (дубликат)
- `422` — валидация (FastAPI автоматически)

## Антипаттерны
- Бизнес-логика в обработчиках → выносить в сервисный слой
- Прямые SQL-запросы в роутерах → использовать репозитории
- Синхронные эндпоинты с блокирующими операциями → `async def`