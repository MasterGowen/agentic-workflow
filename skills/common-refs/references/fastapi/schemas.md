# FastAPI — Pydantic Schemas

## Базовые схемы
```python
from pydantic import BaseModel, ConfigDict
from uuid import UUID
from datetime import datetime

class UserBase(BaseModel):
    name: str
    email: str

class UserCreate(UserBase):
    password: str

class UserUpdate(BaseModel):
    name: str | None = None
    email: str | None = None

class UserResponse(UserBase):
    model_config = ConfigDict(from_attributes=True)
    id: UUID
    created_at: datetime
```

## Валидация
```python
from pydantic import field_validator

class UserCreate(BaseModel):
    email: str

    @field_validator("email")
    @classmethod
    def validate_email(cls, v: str) -> str:
        if "@" not in v:
            raise ValueError("Invalid email")
        return v.lower()
```

## Response Model
```python
@router.get("/{user_id}", response_model=UserResponse)
async def get_user(user_id: UUID):
    ...
```

## Вложенные схемы
```python
class OrderResponse(BaseModel):
    id: UUID
    user: UserResponse  # Вложенная схема
    items: list[OrderItemResponse]
```

## Generic-ответы
```python
from typing import Generic, TypeVar

T = TypeVar("T")

class PaginatedResponse(BaseModel, Generic[T]):
    items: list[T]
    total: int
    offset: int
    limit: int
```

## Антипаттерны
- Использование одной схемы для всего (create/update/response)
- ORM-модели в response_model (всегда Pydantic-схема)
- Секретные поля в response (пароль, токены)