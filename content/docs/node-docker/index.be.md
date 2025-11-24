---
title: "Разгортванне Docker"
weight: 11
---

Гэта кіраўніцтва ахоплівае разгортванне нодаў TALER з выкарыстаннем Docker і Docker Compose.

## Папярэднія патрабаванні

Перад пачаткам пераканайцеся, што ў вас ёсць:

- **Docker**: Версія 20.10 або вышэй ([Усталяваць Docker](https://docs.docker.com/get-docker/))
- **Docker Compose**: Версія 2.0 або вышэй (уключаны ў Docker Desktop)
- **Месца на дыску**: Мінімум 20GB для поўнай ноды
- **RAM**: Мінімум 2GB даступнай памяці (рэкамендуецца 4GB+)

Праверце ўсталёўку:

```bash
docker --version
docker compose version
```

---

## Хуткі старт

### Выкарыстанне Docker Compose

Рэпазіторый TALER уключае гатовы для прадакшна файл `docker-compose.yml`.

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
# Запуск у фонавым рэжыме
docker compose up -d

# Прагляд логаў
docker compose logs -f

# Праверка статусу
docker compose ps

# Спыненне ноды
docker compose down
```

---

## Канфігурацыя

### Паводзіны па змаўчанні

Па змаўчанні кантэйнер Docker TALER:
- Працуе без уключанага RPC сервера (бяспека)
- Адключае функцыянальнасць кашалька
- Захоўвае дадзеныя блокчэйна ў тому `/data`
- Адкрывае P2P порт 23153 і RPC порт 23333

### Выкарыстанне карыстальніцкага файла канфігурацыі

Каб уключыць RPC або наладзіць паводзіны ноды, стварыце файл `taler.conf`:

**taler.conf**:
```ini
# Налады сеткі
listen=1
maxconnections=128

# Налады RPC
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23333
rpcallowip=0.0.0.0/0
rpcbind=0.0.0.0

# Прадукцыйнасць
dbcache=1024
maxmempool=300

# Лагіраванне
printtoconsole=1
```

Раскаментуйце мантаванне канфігурацыі ў `docker-compose.yml`:

```yaml
volumes:
  - taler-data:/data
  - ./taler.conf:/taler.conf:ro  # Uncomment this line
```

Перазапусціце кантэйнер:

```bash
docker compose down
docker compose up -d
```

### Уключэнне кашалька

Каб уключыць функцыянальнасць кашалька:

1. Стварыце каталог для кашалька:
   ```bash
   mkdir wallet
   ```

2. Раскаментуйце том кашалька ў `docker-compose.yml`:
   ```yaml
   volumes:
     - taler-data:/data
     - ./wallet:/wallet  # Uncomment this line
   ```

3. Усталюйце пераменную асяроддзя каталога кашалька:
   ```yaml
   environment:
     - TALER_WALLETDIR=/wallet
   ```

4. Перазапусціце:
   ```bash
   docker compose down
   docker compose up -d
   ```

### Пераменныя асяроддзя

Скрыпт entrypoint падтрымлівае гэтыя пераменныя асяроддзя:

| Пераменная | Апісанне | Па змаўчанні |
|----------|-------------|---------|
| `TALER_DATA` | Каталог дадзеных блокчэйна | `/data` |
| `TALER_CONF` | Шлях да файла канфігурацыі | `/taler.conf` |
| `TALER_WALLETDIR` | Каталог кашалька (уключае кашалёк, калі ўстаноўлена) | не ўстаноўлена (кашалёк адключаны) |

---

## Выкарыстанне Docker Run

Замест Docker Compose можна запусціць напрамую з `docker run`:

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  ghcr.io/abkvme/taler:latest
```

### З карыстальніцкай канфігурацыяй

```bash
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -p 23333:23333 \
  -v taler-data:/data \
  -v $(pwd)/taler.conf:/taler.conf:ro \
  ghcr.io/abkvme/taler:latest
```

### З уключаным кашальком

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

## Маніторынг і кіраванне

### Прагляд логаў

```bash
# Сачыць за логамі
docker compose logs -f

# Апошнія 100 радкоў
docker compose logs --tail 100

# Логі для канкрэтнага сэрвісу
docker logs taler-node
```

### Выкананне каманд

```bash
# Атрымаць інфармацыю аб нодзе (патрабуецца ўключаны RPC)
docker exec taler-node taler-cli getinfo

# Атрымаць інфармацыю аб блокчэйне
docker exec taler-node taler-cli getblockchaininfo

# Атрымаць колькасць злучэнняў
docker exec taler-node taler-cli getconnectioncount
```

### Доступ да абалонкі кантэйнера

```bash
docker exec -it taler-node /bin/bash
```

### Статыстыка кантэйнера

```bash
# Статыстыка ў рэальным часе
docker stats taler-node

# Аднаразовы здымак
docker stats --no-stream taler-node
```

---

## Кіраванне томамі

### Праверка тома

```bash
docker volume inspect taler-data
```

### Рэзервовае капіяванне дадзеных блокчэйна

```bash
# Стварыць рэзервовую копію
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar czf /backup/taler-backup-$(date +%Y%m%d).tar.gz /data
```

### Аднаўленне рэзервовай копіі

```bash
docker run --rm \
  -v taler-data:/data \
  -v $(pwd):/backup \
  ubuntu tar xzf /backup/taler-backup-YYYYMMDD.tar.gz -C /
```

### Выкарыстанне каталога хоста

Замест тома Docker выкарыстоўвайце каталог хоста:

```yaml
volumes:
  - ./data:/data  # Host directory
```

Гэта захоўвае дадзеныя блокчэйна ў `./data` на вашай хост-сістэме.

---

## Стварэнне карыстальніцкага вобраза

Рэпазіторый TALER уключае `Dockerfile` для стварэння карыстальніцкіх вобразаў.

### Лакальная зборка

```bash
# Кланаваць рэпазіторый
git clone https://github.com/abkvme/taler.git
cd taler

# Сабраць вобраз
docker build -t taler:local .

# Запусціць лакальную зборку
docker run -d \
  --name taler-node \
  -p 23153:23153 \
  -v taler-data:/data \
  taler:local
```

### Мультыархітэктурная зборка

Dockerfile падтрымлівае `linux/amd64` і `linux/arm64`:

```bash
# Сабраць для некалькіх платформаў
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t taler:custom \
  .
```

---

## Канфігурацыя Healthcheck

Каб уключыць маніторынг здароўя, пераканайцеся, што RPC уключаны ў вашым `taler.conf`, затым раскаментуйце секцыю healthcheck у `docker-compose.yml`:

```yaml
healthcheck:
  test: ["CMD", "taler-cli", "getblockchaininfo"]
  interval: 60s
  timeout: 30s
  retries: 5
  start_period: 1800s  # 30 minutes for initial sync
```

Праверце статус здароўя:

```bash
docker inspect --format='{{.State.Health.Status}}' taler-node
```

---

## Лепшыя практыкі бяспекі

### Прывязка RPC порта

Для бяспекі прывязвайце RPC порт толькі да localhost, калі гэта магчыма:

```yaml
ports:
  - "23153:23153"
  - "127.0.0.1:23333:23333"  # Localhost only
```

### Моцныя ўліковыя дадзеныя RPC

Генеруйце моцныя паролі:

```bash
openssl rand -base64 32
```

### Правы доступу да файлаў

Абараніце свой файл канфігурацыі:

```bash
chmod 600 taler.conf
```

### Падтрымлівайце вобразы ў актуальным стане

Рэгулярна абнаўляйце да апошняга вобраза:

```bash
docker compose pull
docker compose up -d
```

---

## Ліквідацыя непаладак

### Кантэйнер не запускаецца

```bash
# Праверце логі на памылкі
docker compose logs

# Праверце статус кантэйнера
docker compose ps -a

# Выдаліце і перастварыце
docker compose down
docker compose up -d
```

### Праблемы з падключэннем RPC

Калі каманды `taler-cli` не працуюць:

1. Пераканайцеся, што RPC уключаны ў `taler.conf` з `server=1`
2. Праверце, што файл канфігурацыі змантаваны: `docker exec taler-node ls -la /taler.conf`
3. Праверце, што ўліковыя дадзеныя RPC правільныя

### Праблемы сінхранізацыі блокчэйна

```bash
# Праверце прагрэс сінхранізацыі
docker exec taler-node taler-cli getblockchaininfo

# Прагледзьце адладачны вывад у логах
docker compose logs -f
```

### Скід дадзеных блокчэйна

```bash
# Спыніце кантэйнер
docker compose down

# Выдаліце том (УВАГА: выдаляе ўсе дадзеныя блокчэйна)
docker volume rm taler-data

# Перазапусціце
docker compose up -d
```

---

## Прадакшн разгортванне

### Запуск як сістэмнага сэрвісу

Стварыце `/etc/systemd/system/taler-docker.service`:

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

Уключыце і запусціце:

```bash
sudo systemctl enable taler-docker
sudo systemctl start taler-docker
```

### Аўтаматычныя абнаўленні

Выкарыстоўвайце Watchtower для аўтаабнаўлення кантэйнераў:

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

## Наступныя крокі

- **Node CLI**: Вывучыце аперацыі каманднага радка ў [Node CLI](/en/docs/node-cli/)
- **Майнінг і стэйкінг**: Наладзьце майнінг/стэйкінг у [Майнінг і стэйкінг](/en/docs/node-mining/)
- **RPC API**: Стварайце прыкладанні з [Дакументацыя для распрацоўшчыкаў](/en/docs/rpc-reference/)
