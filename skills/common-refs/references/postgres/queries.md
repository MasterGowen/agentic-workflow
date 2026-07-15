# PostgreSQL — Queries

## EXPLAIN ANALYZE
- Всегда используй `EXPLAIN ANALYZE`, не просто `EXPLAIN`
- Смотри на: actual time, rows, loops, планируемые vs фактические строки
- `BUFFERS` — показывает попадания в кэш: `EXPLAIN (ANALYZE, BUFFERS) SELECT ...`

## Оптимизация запросов
- **N+1**: замени цикл запросов на JOIN или `WHERE IN`
- **SELECT \***: бери только нужные колонки
- **COUNT(\*)**: используй оценку (`EXPLAIN`) или материализованное представление для больших таблиц
- **OFFSET**: замени на keyset pagination (`WHERE id > last_id LIMIT n`)
- **DISTINCT**: проверь, действительно ли нужен (часто симптом плохого JOIN)

## JOIN
- `INNER JOIN` — только совпадающие строки
- `LEFT JOIN` — все строки из левой таблицы
- `LATERAL` — подзапрос ссылается на колонки внешнего запроса
- Порядок JOIN: от меньшего результата к большему

## Common Table Expressions (CTE)
```sql
WITH active_users AS (
    SELECT id FROM users WHERE last_login > now() - INTERVAL '30 days'
)
SELECT * FROM orders WHERE user_id IN (SELECT id FROM active_users);
```
- `MATERIALIZED` / `NOT MATERIALIZED` — управление оптимизатором (PG 12+)

## Подготовленные запросы
- Использовать `PREPARE` / `EXECUTE` для часто выполняемых запросов
- В коде: параметризованные запросы через драйвер (`$1`, `%s`)

## Антипаттерны
- Циклы в коде приложения вместо одного SQL-запроса
- `ILIKE '%pattern%'` на больших таблицах → использовать full-text search
- Отсутствие `LIMIT` на потенциально больших выборках