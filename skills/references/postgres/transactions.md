# PostgreSQL — Transactions

## Уровни изоляции
- **Read Committed** (по умолчанию): видит только закоммиченные данные, но возможны non-repeatable read и phantoms
- **Repeatable Read**: snapshot на момент начала транзакции, нет non-repeatable read, но возможны phantoms (в PG — нет, из-за реализации)
- **Serializable**: полная изоляция, может потребовать retry при конфликтах

## Когда повышать изоляцию
- Финансовые операции (баланс, переводы)
- Бронирование (билеты, слоты)
- Любая операция, где состояние зависит от текущего значения

## Паттерны
```python
async with db.transaction():
    # Все операции внутри — атомарны
    await db.execute(...)
    await db.execute(...)
    # При исключении — автоматический rollback
```

## Pessimistic Locking
```sql
-- Блокировка строки для обновления
SELECT * FROM orders WHERE id = $1 FOR UPDATE;
-- Блокировка без ожидания
SELECT * FROM orders WHERE id = $1 FOR UPDATE NOWAIT;
-- Пропуск заблокированных строк
SELECT * FROM orders WHERE status = 'pending' FOR UPDATE SKIP LOCKED LIMIT 1;
```

## Optimistic Locking
- Добавить колонку `version INTEGER DEFAULT 1`
- При обновлении: `UPDATE ... SET version = version + 1 WHERE version = $old_version`
- Если затронуто 0 строк — конфликт, повторить

## Deadlocks
- Всегда бери блокировки в одном порядке
- Используй таймауты: `lock_timeout = '2s'`
- Логируй deadlock-и: `log_lock_waits = on`

## Антипаттерны
- Длинные транзакции (держат блокировки, замедляют vacuum)
- Транзакции с внешними вызовами (HTTP, очереди)
- Автокоммит для операций, требующих атомарности