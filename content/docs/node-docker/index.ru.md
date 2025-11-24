---
title: "Развертывание Docker"
weight: 11
---

Это руководство охватывает развертывание нод TALER с использованием Docker и Docker Compose.

## Предварительные требования

Перед началом убедитесь, что у вас есть:

- **Docker**: Версия 20.10 или выше ([Установить Docker](https://docs.docker.com/get-docker/))
- **Docker Compose**: Версия 2.0 или выше (включен в Docker Desktop)
- **Место на диске**: Минимум 20GB для полной ноды
- **RAM**: Минимум 2GB доступной памяти (рекомендуется 4GB+)

Проверьте установку:

```bash
docker --version
docker compose version
```

---

## Быстрый старт

### Использование Docker Compose

Репозиторий TALER включает готовый для продакшна файл `docker-compose.yml`.

**docker-compose.yml**:

```yaml
services:
  taler:
    image: ghcr.io/abkvme/taler:latest
    container_name: taler-node
    restart: unless-stopped

    ports:
      - "23153:23153"  # P2P network port
      - "23333:23333"  # RPC port

    volumes:
      - taler-data:/data  # Or use host path: ./data:/data
      # - ./taler.conf:/taler.conf:ro  # Uncomment to use custom config file
      # - ./wallet:/wallet  # Uncomment to enable wallet (also set TALER_WALLETDIR=/wallet in .env)

    # Healthcheck disabled by default (requires RPC to be enabled via custom taler.conf)
    # healthcheck:
    #   test: ["CMD", "taler-cli", "getblockchaininfo"]
    #   interval: 60s
    #   timeout: 30s
    #   retries: 5
    #   start_period: 1800s

    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"

volumes:
  taler-data:
    driver: local
```

### Запуск ноды

```bash
# Запуск в фоновом режиме
docker compose up -d

# Просмотр логов
docker compose logs -f

# Проверка статуса
docker compose ps

# Остановка ноды
docker compose down
```

---

## Конфигурация

### Поведение по умолчанию

По умолчанию контейнер Docker TALER:
- Работает без включенного RPC сервера (безопасность)
- Отключает функциональность кошелька
- Хранит данные блокчейна в томе `/data`
- Открывает P2P порт 23153 и RPC порт 23333

### Использование пользовательского файла конфигурации

Чтобы включить RPC или настроить поведение ноды, создайте файл `taler.conf`:

**taler.conf**:
```ini
# Настройки сети
listen=1
maxconnections=128

# Настройки RPC
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23333
rpcallowip=0.0.0.0/0
rpcbind=0.0.0.0

# Производительность
dbcache=1024
maxmempool=300

# Логирование
printtoconsole=1
```

Раскомментируйте монтирование конфигурации в `docker-compose.yml`:

```yaml
volumes:
  - taler-data:/data
  - ./taler.conf:/taler.conf:ro  # Uncomment this line
```

Перезапустите контейнер:

```bash
docker compose down
docker compose up -d
```

### Включение кошелька

Чтобы включить функциональность кошелька:

1. Создайте каталог для кошелька:
   ```bash
   mkdir wallet
   ```

2. Раскомментируйте том кошелька в `docker-compose.yml`:
   ```yaml
   volumes:
     - taler-data:/data
     - ./wallet:/wallet  # Uncomment this line
   ```

3. Установите переменную окружения каталога кошелька:
   ```yaml
   environment:
     - TALER_WALLETDIR=/wallet
   ```

4. Перезапустите:
   ```bash
   docker compose down
   docker compose up -d
   ```

### Переменные окружения

Скрипт entrypoint поддерживает эти переменные окружения:

| Переменная | Описание | По умолчанию |
|----------|-------------|---------|
| `TALER_DATA` | Каталог данных блокчейна | `/data` |
| `TALER_CONF` | Путь к файлу конфигурации | `/taler.conf` |
| `TALER_WALLETDIR` | Каталог кошелька (включает кошелёк, если установлено) | не установлено (кошелёк отключен) |

---

## Использование Docker Run

Вместо Docker Compose можно запустить напрямую с `docker run`:

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  ghcr.io/abkvme/taler:latest
```

### С пользовательской конфигурацией

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  -v $(pwd)/taler.conf:/taler.conf:ro \
  ghcr.io/abkvme/taler:latest
```

### С включенным кошельком

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  -v $(pwd)/wallet:/wallet \
  -e TALER_WALLETDIR=/wallet \
  ghcr.io/abkvme/taler:latest
```

---

## Мониторинг и управление

### Просмотр логов

```bash
# Следить за логами
docker compose logs -f

# Последние 100 строк
docker compose logs --tail 100

# Логи для конкретного сервиса
docker logs taler-node
```

### Выполнение команд

```bash
# Получить информацию о ноде (требуется включенный RPC)
docker exec taler-node taler-cli getinfo

# Получить информацию о блокчейне
docker exec taler-node taler-cli getblockchaininfo

# Получить количество соединений
docker exec taler-node taler-cli getconnectioncount
```

### Доступ к оболочке контейнера

```bash
docker exec -it taler-node /bin/bash
```

### Статистика контейнера

```bash
# Статистика в реальном времени
docker stats taler-node

# Одноразовый снимок
docker stats --no-stream taler-node
```

---

## Управление томами

### Проверка тома

```bash
docker volume inspect taler-data
```

### Резервное копирование данных блокчейна

```bash
# Создать резервную копию
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar czf /backup/taler-backup-$(date +%Y%m%d).tar.gz /data
```

### Восстановление резервной копии

```bash
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar xzf /backup/taler-backup-YYYYMMDD.tar.gz -C /
```

### Использование каталога хоста

Вместо тома Docker используйте каталог хоста:

```yaml
volumes:
  - ./data:/data  # Host directory
```

Это сохраняет данные блокчейна в `./data` на вашей хост-системе.

---

## Создание пользовательского образа

Репозиторий TALER включает `Dockerfile` для создания пользовательских образов.

### Локальная сборка

```bash
# Клонировать репозиторий
git clone https://github.com/abkvme/taler.git
cd taler

# Собрать образ
docker build -t taler:local .

# Запустить локальную сборку
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -v taler-data:/data \
  taler:local
```

### Мультиархитектурная сборка

Dockerfile поддерживает `linux/amd64` и `linux/arm64`:

```bash
# Собрать для нескольких платформ
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t taler:custom \
  .
```

---

## Конфигурация Healthcheck

Чтобы включить мониторинг здоровья, убедитесь, что RPC включен в вашем `taler.conf`, затем раскомментируйте секцию healthcheck в `docker-compose.yml`:

```yaml
healthcheck:
  test: ["CMD", "taler-cli", "getblockchaininfo"]
  interval: 60s
  timeout: 30s
  retries: 5
  start_period: 1800s  # 30 minutes for initial sync
```

Проверьте статус здоровья:

```bash
docker inspect --format='{{.State.Health.Status}}' taler-node
```

---

## Лучшие практики безопасности

### Привязка RPC порта

Для безопасности привязывайте RPC порт только к localhost, когда это возможно:

```yaml
ports:
  - "23153:23153"
  - "127.0.0.1:23333:23333"  # Localhost only
```

### Сильные учетные данные RPC

Генерируйте сильные пароли:

```bash
openssl rand -base64 32
```

### Права доступа к файлам

Защитите свой файл конфигурации:

```bash
chmod 600 taler.conf
```

### Поддерживайте образы в актуальном состоянии

Регулярно обновляйте до последнего образа:

```bash
docker compose pull
docker compose up -d
```

---

## Устранение неполадок

### Контейнер не запускается

```bash
# Проверьте логи на ошибки
docker compose logs

# Проверьте статус контейнера
docker compose ps -a

# Удалите и пересоздайте
docker compose down
docker compose up -d
```

### Проблемы с подключением RPC

Если команды `taler-cli` не работают:

1. Убедитесь, что RPC включен в `taler.conf` с `server=1`
2. Проверьте, что файл конфигурации смонтирован: `docker exec taler-node ls -la /taler.conf`
3. Проверьте, что учетные данные RPC правильные

### Проблемы синхронизации блокчейна

```bash
# Проверьте прогресс синхронизации
docker exec taler-node taler-cli getblockchaininfo

# Просмотрите отладочный вывод в логах
docker compose logs -f
```

### Сброс данных блокчейна

```bash
# Остановите контейнер
docker compose down

# Удалите том (ВНИМАНИЕ: удаляет все данные блокчейна)
docker volume rm taler-data

# Перезапустите
docker compose up -d
```

---

## Продакшн развертывание

### Запуск как системного сервиса

Создайте `/etc/systemd/system/taler-docker.service`:

```ini
[Unit]
Description=TALER Docker Node
Requires=docker.service
After=docker.service

[Service]
Type=oneshot
RemainAfterExit=yes
WorkingDirectory=/opt/taler
ExecStart=/usr/bin/docker compose up -d
ExecStop=/usr/bin/docker compose down
TimeoutStartSec=0

[Install]
WantedBy=multi-user.target
```

Включите и запустите:

```bash
sudo systemctl enable taler-docker
sudo systemctl start taler-docker
```

### Автоматические обновления

Используйте Watchtower для автообновления контейнеров:

```yaml
services:
  taler:
    # ... existing config ...

  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --interval 86400  # Check daily
```

---

## Следующие шаги

- **Node CLI**: Изучите операции командной строки в [Node CLI](/en/docs/node-cli/)
- **Майнинг и стейкинг**: Настройте майнинг/стейкинг в [Майнинг и стейкинг](/en/docs/node-mining/)
- **RPC API**: Создавайте приложения с [Документация для разработчиков](/en/docs/rpc-reference/)
