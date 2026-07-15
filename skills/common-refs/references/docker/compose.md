# Docker — Compose

## Базовая структура
```yaml
# docker-compose.yml
version: "3.8"

services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      db:
        condition: service_healthy
    volumes:
      - ./src:/app/src  # Для разработки

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d mydb"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  pgdata:
```

## Сети
```yaml
services:
  app:
    networks:
      - frontend
      - backend
  db:
    networks:
      - backend

networks:
  frontend:
  backend:
    internal: true  # Нет доступа извне
```

## Переопределение для окружений
```bash
# Продакшн
docker compose -f docker-compose.yml -f docker-compose.prod.yml up
```

## Команды
```bash
docker compose up -d          # Запуск в фоне
docker compose down           # Остановка и удаление
docker compose down -v        # + удаление volumes
docker compose logs -f app    # Логи сервиса
docker compose exec app bash  # Зайти в контейнер
docker compose build --no-cache  # Пересборка
```

## Антипаттерны
- Секреты в `environment` (используй `env_file` или secrets)
- `depends_on` без healthcheck (контейнер запущен ≠ сервис готов)
- Проброс портов БД наружу в продакшене
- Жёстко закодированные пароли