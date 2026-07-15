# Python — Typing

## Базовые типы
```python
from typing import Optional, Union, Any

name: str = "hello"
count: int = 0
active: bool = True
data: dict[str, int] = {"a": 1}
items: list[str] = ["a", "b"]
maybe: str | None = None  # Python 3.10+
```

## Dataclasses
```python
from dataclasses import dataclass, field

@dataclass
class User:
    name: str
    email: str
    tags: list[str] = field(default_factory=list)
```

## Pydantic (предпочтительно для данных)
```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    email: str
    age: int = 0
```

## Generic
```python
from typing import TypeVar, Generic

T = TypeVar("T")

class Repository(Generic[T]):
    async def get(self, id: UUID) -> T | None:
        ...

class UserRepository(Repository[User]):
    ...
```

## Protocol (структурная типизация)
```python
from typing import Protocol

class HasId(Protocol):
    id: UUID

def process(entity: HasId) -> None:
    print(entity.id)
```

## Callable
```python
from collections.abc import Callable

Handler = Callable[[int, str], bool]  # (int, str) -> bool
```

## Антипаттерны
- `Any` — почти всегда можно заменить на конкретный тип или Generic
- `# type: ignore` без комментария почему
- Смешивание dataclass и Pydantic без необходимости