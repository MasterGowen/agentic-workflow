# Python — Async / Await

## Базовый синтаксис
```python
import asyncio

async def fetch_data(url: str) -> dict:
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.json()

# Запуск
asyncio.run(fetch_data("https://api.example.com"))
```

## Параллельное выполнение
```python
# Одновременные запросы
results = await asyncio.gather(
    fetch_data(url1),
    fetch_data(url2),
    fetch_data(url3),
)

# Очередь задач
tasks = [asyncio.create_task(fetch_data(url)) for url in urls]
results = await asyncio.gather(*tasks)
```

## Таймауты
```python
try:
    async with asyncio.timeout(5.0):
        result = await slow_operation()
except TimeoutError:
    ...
```

## Синхронизация
```python
lock = asyncio.Lock()

async with lock:
    await critical_section()
```

## FastAPI + async
```python
@router.get("/")
async def endpoint(db: AsyncSession = Depends(get_db)):
    result = await db.execute(select(User))
    return result.scalars().all()
```

## Антипаттерны
- `time.sleep()` в async-функции → `await asyncio.sleep()`
- Блокирующие вызовы в async-контексте (CPU-bound, синхронный I/O)
- `asyncio.run()` внутри уже запущенного event loop
- Создание задач без сохранения ссылки (могут быть отменены GC)

## Когда НЕ использовать async
- CPU-bound задачи (используй `run_in_executor` или отдельный процесс)
- Простые скрипты без I/O
- Библиотеки без async-поддержки (используй `run_in_executor`)