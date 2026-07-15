# PostgreSQL — Schema / DDL

## Таблицы
- Именование: `snake_case`, множественное число (`users`, `orders`)
- Первичный ключ: `id UUID PRIMARY KEY DEFAULT gen_random_uuid()`
- Внешний ключ: `user_id UUID REFERENCES users(id)`
- Timestamps: `created_at TIMESTAMPTZ DEFAULT now()`, `updated_at TIMESTAMPTZ`

## Связи
- **One-to-Many**: FK на стороне many
- **Many-to-Many**: промежуточная таблица `a_b` с составным PK
- **Self-referencing**: FK на ту же таблицу (для деревьев/иерархий)

## Миграции
- Использовать Alembic
- Каждая миграция — один логический шаг
- Всегда писать downgrade
- Не править старые миграции — создавать новые

## Типы данных
- `UUID` для идентификаторов
- `TEXT` вместо `VARCHAR` (нет разницы в PostgreSQL)
- `JSONB` для полуструктурированных данных
- `ENUM` только если значения фиксированы и не будут меняться
- `NUMERIC` для денег, `DOUBLE PRECISION` для метрик

## Пример
```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    status TEXT NOT NULL DEFAULT 'pending',
    total NUMERIC(10, 2) NOT NULL,
    metadata JSONB DEFAULT '{}',
    created_at TIMESTAMPTZ DEFAULT now(),
    updated_at TIMESTAMPTZ DEFAULT now()
);
```