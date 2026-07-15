# FastAPI — Dependency Injection

## Depends
```python
from fastapi import Depends

@router.get("/")
async def list_users(
    db: AsyncSession = Depends(get_db),
    current_user: User = Depends(get_current_user),
):
    ...
```

## Цепочки зависимостей
```python
def get_db() -> AsyncSession:
    ...

def get_repo(db: AsyncSession = Depends(get_db)) -> UserRepository:
    return UserRepository(db)

@router.get("/")
async def list_users(repo: UserRepository = Depends(get_repo)):
    ...
```

## Глобальные зависимости
```python
app = FastAPI(dependencies=[Depends(verify_api_key)])
```

## Генераторы (yield)
```python
async def get_db():
    async with async_session() as session:
        yield session
        # Код после yield — cleanup
```

## Область видимости
- По умолчанию: новый экземпляр на каждый запрос
- Кэширование в рамках запроса: если зависимость используется несколько раз, создаётся один раз

## Типовые зависимости
- `get_db` — сессия БД
- `get_current_user` — аутентификация
- `get_settings` — конфигурация
- `get_cache` — Redis-клиент

## Антипаттерны
- Слишком глубокая вложенность зависимостей (>3 уровней)
- Тяжёлая инициализация в Depends (должна быть быстрой)
- Побочные эффекты в зависимостях (изменение состояния)