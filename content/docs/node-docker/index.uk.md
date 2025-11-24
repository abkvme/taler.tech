---
title: "Розгортання Docker"
weight: 11
---

Цей посібник охоплює розгортання нод TALER з використанням Docker і Docker Compose.

## Передумови

Перед початком переконайтеся, що у вас є:

- **Docker**: Версія 20.10 або вище ([Встановити Docker](https://docs.docker.com/get-docker/))
- **Docker Compose**: Версія 2.0 або вище (включений у Docker Desktop)
- **Місце на диску**: Мінімум 20GB для повної ноди
- **RAM**: Мінімум 2GB доступної пам'яті (рекомендується 4GB+)

Перевірте встановлення:

```bash
docker --version
docker compose version
```

---

## Швидкий старт

### Використання Docker Compose

Репозиторій TALER включає готовий для продакшну файл `docker-compose.yml`.

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

### Запуск ноди

```bash
# Запуск у фоновому режимі
docker compose up -d

# Перегляд логів
docker compose logs -f

# Перевірка статусу
docker compose ps

# Зупинка ноди
docker compose down
```

---

## Конфігурація

### Поведінка за замовчуванням

За замовчуванням контейнер Docker TALER:
- Працює без увімкненого RPC сервера (безпека)
- Вимикає функціональність гаманця
- Зберігає дані блокчейну в томі `/data`
- Відкриває P2P порт 23153 і RPC порт 23333

### Використання користувацького файлу конфігурації

Щоб увімкнути RPC або налаштувати поведінку ноди, створіть файл `taler.conf`:

**taler.conf**:
```ini
# Налаштування мережі
listen=1
maxconnections=128

# Налаштування RPC
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23333
rpcallowip=0.0.0.0/0
rpcbind=0.0.0.0

# Продуктивність
dbcache=1024
maxmempool=300

# Логування
printtoconsole=1
```

Розкоментуйте монтування конфігурації в `docker-compose.yml`:

```yaml
volumes:
  - taler-data:/data
  - ./taler.conf:/taler.conf:ro  # Uncomment this line
```

Перезапустіть контейнер:

```bash
docker compose down
docker compose up -d
```

### Увімкнення гаманця

Щоб увімкнути функціональність гаманця:

1. Створіть каталог для гаманця:
   ```bash
   mkdir wallet
   ```

2. Розкоментуйте том гаманця в `docker-compose.yml`:
   ```yaml
   volumes:
     - taler-data:/data
     - ./wallet:/wallet  # Uncomment this line
   ```

3. Встановіть змінну оточення каталогу гаманця:
   ```yaml
   environment:
     - TALER_WALLETDIR=/wallet
   ```

4. Перезапустіть:
   ```bash
   docker compose down
   docker compose up -d
   ```

### Змінні оточення

Скрипт entrypoint підтримує ці змінні оточення:

| Змінна | Опис | За замовчуванням |
|----------|-------------|---------|
| `TALER_DATA` | Каталог даних блокчейну | `/data` |
| `TALER_CONF` | Шлях до файлу конфігурації | `/taler.conf` |
| `TALER_WALLETDIR` | Каталог гаманця (вмикає гаманець, якщо встановлено) | не встановлено (гаманець вимкнено) |

---

## Використання Docker Run

Замість Docker Compose можна запустити безпосередньо з `docker run`:

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  ghcr.io/abkvme/taler:latest
```

### З користувацькою конфігурацією

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  -v $(pwd)/taler.conf:/taler.conf:ro \
  ghcr.io/abkvme/taler:latest
```

### З увімкненим гаманцем

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

## Моніторинг та управління

### Перегляд логів

```bash
# Стежити за логами
docker compose logs -f

# Останні 100 рядків
docker compose logs --tail 100

# Логи для конкретного сервісу
docker logs taler-node
```

### Виконання команд

```bash
# Отримати інформацію про ноду (потрібен увімкнений RPC)
docker exec taler-node taler-cli getinfo

# Отримати інформацію про блокчейн
docker exec taler-node taler-cli getblockchaininfo

# Отримати кількість з'єднань
docker exec taler-node taler-cli getconnectioncount
```

### Доступ до оболонки контейнера

```bash
docker exec -it taler-node /bin/bash
```

### Статистика контейнера

```bash
# Статистика в реальному часі
docker stats taler-node

# Одноразовий знімок
docker stats --no-stream taler-node
```

---

## Управління томами

### Перевірка тома

```bash
docker volume inspect taler-data
```

### Резервне копіювання даних блокчейну

```bash
# Створити резервну копію
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar czf /backup/taler-backup-$(date +%Y%m%d).tar.gz /data
```

### Відновлення резервної копії

```bash
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar xzf /backup/taler-backup-YYYYMMDD.tar.gz -C /
```

### Використання каталогу хоста

Замість тома Docker використовуйте каталог хоста:

```yaml
volumes:
  - ./data:/data  # Host directory
```

Це зберігає дані блокчейну в `./data` на вашій хост-системі.

---

## Створення користувацького образу

Репозиторій TALER включає `Dockerfile` для створення користувацьких образів.

### Локальна збірка

```bash
# Клонувати репозиторій
git clone https://github.com/abkvme/taler.git
cd taler

# Зібрати образ
docker build -t taler:local .

# Запустити локальну збірку
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -v taler-data:/data \
  taler:local
```

### Мультиархітектурна збірка

Dockerfile підтримує `linux/amd64` і `linux/arm64`:

```bash
# Зібрати для кількох платформ
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t taler:custom \
  .
```

---

## Конфігурація Healthcheck

Щоб увімкнути моніторинг здоров'я, переконайтеся, що RPC увімкнено у вашому `taler.conf`, потім розкоментуйте секцію healthcheck в `docker-compose.yml`:

```yaml
healthcheck:
  test: ["CMD", "taler-cli", "getblockchaininfo"]
  interval: 60s
  timeout: 30s
  retries: 5
  start_period: 1800s  # 30 minutes for initial sync
```

Перевірте статус здоров'я:

```bash
docker inspect --format='{{.State.Health.Status}}' taler-node
```

---

## Кращі практики безпеки

### Прив'язка RPC порту

Для безпеки прив'язуйте RPC порт тільки до localhost, коли це можливо:

```yaml
ports:
  - "23153:23153"
  - "127.0.0.1:23333:23333"  # Localhost only
```

### Сильні облікові дані RPC

Генеруйте сильні паролі:

```bash
openssl rand -base64 32
```

### Права доступу до файлів

Захистіть свій файл конфігурації:

```bash
chmod 600 taler.conf
```

### Підтримуйте образи в актуальному стані

Регулярно оновлюйте до останнього образу:

```bash
docker compose pull
docker compose up -d
```

---

## Усунення неполадок

### Контейнер не запускається

```bash
# Перевірте логи на помилки
docker compose logs

# Перевірте статус контейнера
docker compose ps -a

# Видаліть і перестворіть
docker compose down
docker compose up -d
```

### Проблеми з підключенням RPC

Якщо команди `taler-cli` не працюють:

1. Переконайтеся, що RPC увімкнено в `taler.conf` з `server=1`
2. Перевірте, що файл конфігурації змонтовано: `docker exec taler-node ls -la /taler.conf`
3. Перевірте, що облікові дані RPC правильні

### Проблеми синхронізації блокчейну

```bash
# Перевірте прогрес синхронізації
docker exec taler-node taler-cli getblockchaininfo

# Перегляньте відладковий вивід у логах
docker compose logs -f
```

### Скидання даних блокчейну

```bash
# Зупиніть контейнер
docker compose down

# Видаліть том (УВАГА: видаляє всі дані блокчейну)
docker volume rm taler-data

# Перезапустіть
docker compose up -d
```

---

## Продакшн розгортання

### Запуск як системного сервісу

Створіть `/etc/systemd/system/taler-docker.service`:

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

Увімкніть та запустіть:

```bash
sudo systemctl enable taler-docker
sudo systemctl start taler-docker
```

### Автоматичні оновлення

Використовуйте Watchtower для автооновлення контейнерів:

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

## Наступні кроки

- **Node CLI**: Вивчіть операції командного рядка в [Node CLI](/en/docs/node-cli/)
- **Майнінг і стейкінг**: Налаштуйте майнінг/стейкінг в [Майнінг і стейкінг](/en/docs/node-mining/)
- **RPC API**: Створюйте додатки з [Документація для розробників](/en/docs/rpc-reference/)
