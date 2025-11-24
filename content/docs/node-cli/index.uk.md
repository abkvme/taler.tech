---
title: "Командний рядок (talerd)"
weight: 10
---

Цей посібник містить повну документацію по інструментах `talerd` (демон TALER) і `taler-cli` (інтерфейс командного рядка).

## Огляд

TALER надає два основні інструменти командного рядка:

- **`talerd`**: Демон блокчейну, який запускає повну ноду
- **`taler-cli`**: Інструмент командного рядка для взаємодії з працюючим демоном

---

## talerd - Демон TALER

Демон `talerd` є основним програмним забезпеченням ноди блокчейну TALER.

### Базове використання

```bash
talerd [опції]
```

### Основні опції

#### Налаштування мережі

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-listen` | Приймати вхідні з'єднання | 1 (увімкнено) |
| `-port=<port>` | Слухати на вказаному порту | 23153 (mainnet) |
| `-maxconnections=<n>` | Максимальна кількість з'єднань з пірами | 128 |
| `-addnode=<ip>` | Додати ноду для підключення | - |
| `-connect=<ip>` | Підключатися тільки до вказаної ноди | - |
| `-seednode=<ip>` | Підключитися до ноди для отримання початкових пірів | - |
| `-proxy=<ip:port>` | Підключатися через SOCKS5 проксі | - |
| `-bind=<addr>` | Прив'язатися до конкретної адреси | Всі інтерфейси |

#### Керування даними

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-datadir=<dir>` | Вказати каталог даних | Див. нижче |
| `-prune=<n>` | Увімкнути обрізку (зменшити використання диска до ~n MB) | 0 (вимкнено) |
| `-dbcache=<n>` | Розмір кешу бази даних у MB | 450 |
| `-maxmempool=<n>` | Максимальний розмір mempool у MB | 300 |
| `-blocksdir=<dir>` | Вказати каталог для зберігання блоків | datadir/blocks |

**Каталоги даних за замовчуванням**:
- Windows: `%APPDATA%\TALER\`
- macOS: `~/Taler/`
- Linux: `~/.taler/`

#### RPC сервер

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-server` | Приймати JSON-RPC команди | 0 (вимкнено) |
| `-rpcuser=<user>` | Ім'я користувача RPC | - |
| `-rpcpassword=<pw>` | Пароль RPC | - |
| `-rpcport=<port>` | Слухати RPC на порту | 23152 (mainnet) |
| `-rpcbind=<addr>` | Прив'язати RPC до адреси | 127.0.0.1 |
| `-rpcallowip=<ip>` | Дозволити RPC з IP (можна використовувати кілька разів) | 127.0.0.1 |

#### Майнінг і стейкінг

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-gen` | Генерувати/майнити монети | 0 (вимкнено) |
| `-genproclimit=<n>` | Встановити потоки майнінгу (-1 = всі ядра) | -1 |
| `-stakegen` | Увімкнути генерацію стейків (PoS) | 1 (увімкнено) |
| `-reservebalance=<amt>` | Зарезервована сума, не використовувана для стейкінгу | 0 |

#### Блокчейн і синхронізація

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-testnet` | Використовувати тестову мережу | 0 (mainnet) |
| `-reindex` | Перебудувати індекс блокчейну з файлів blk*.dat | 0 |
| `-reindex-chainstate` | Перебудувати chainstate з поточних блоків | 0 |
| `-txindex` | Підтримувати повний індекс транзакцій | 0 |
| `-addressindex` | Підтримувати індекс транзакцій на основі адрес | 0 |
| `-timestampindex` | Підтримувати індекс блоків на основі часових міток | 0 |
| `-spentindex` | Підтримувати повний індекс витрачених виходів | 0 |

#### Гаманець

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-disablewallet` | Не завантажувати гаманець | 0 (гаманець увімкнено) |
| `-wallet=<file>` | Вказати файл гаманця | wallet.dat |
| `-zapwallettxes=<mode>` | Видалити всі транзакції гаманця (1=зберегти метадані, 2=видалити) | 0 |
| `-upgradewallet` | Оновити гаманець до останнього формату | 0 |
| `-salvagewallet` | Спробувати відновити приватні ключі з пошкодженого гаманця | 0 |

#### Логування та відладка

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-debug=<category>` | Увімкнути відладкове логування (net, tor, mempool, rpc, bench, db) | 0 |
| `-debuglogfile=<file>` | Вказати розташування файлу відладкового логу | debug.log |
| `-printtoconsole` | Відправляти інформацію трасування/відладки в консоль | 0 |
| `-logtimestamps` | Додавати часові мітки до відладкового виводу | 1 |
| `-shrinkdebugfile` | Стискати debug.log при запуску | 1 |

#### Продуктивність

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-par=<n>` | Встановити потоки перевірки скриптів (-1 = авто) | -1 |
| `-checkblocks=<n>` | Кількість блоків для перевірки при запуску | 6 |
| `-checklevel=<n>` | Ретельність перевірки блоків (0-4) | 3 |

#### Керування демоном

| Опція | Опис |
|--------|-------------|
| `-daemon` | Запустити у фоновому режимі як демон (тільки Unix) |
| `-pid=<file>` | Вказати розташування PID файлу |
| `-conf=<file>` | Вказати файл конфігурації |
| `-version` | Вивести інформацію про версію |
| `-help` | Показати довідкове повідомлення |

---

## Приклади використання talerd

### Запуск повної ноди з увімкненим RPC

```bash
talerd -server -rpcuser=myuser -rpcpassword=mypassword
```

### Запуск обрізаної ноди (економія місця на диску)

```bash
talerd -prune=2000
```

Зберігає приблизно 2GB даних блокчейну.

### Запуск у testnet

```bash
talerd -testnet
```

### Увімкнення PoW майнінгу

```bash
talerd -gen -genproclimit=4
```

Майнінг з використанням 4 ядер CPU.

### Запуск як фонового демона (Linux/macOS)

```bash
talerd -daemon
```

### Запуск як systemd сервіс (Linux)

Для розгортання на продакшн серверах запуск `talerd` як systemd сервісу забезпечує автоматичний запуск, контроль і логування.

**Крок 1: Створити файл systemd сервісу**

Створіть `/etc/systemd/system/talerd.service`:

```ini
[Unit]
Description=TALER Daemon
After=network.target

[Service]
Type=forking
User=taler
Group=taler
ExecStart=/usr/local/bin/talerd -daemon
ExecStop=/usr/local/bin/taler-cli stop
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

**Крок 2: Створити користувача taler** (якщо не існує):

```bash
sudo useradd -r -m -s /bin/bash taler
```

**Крок 3: Встановити права**:

```bash
sudo chown -R taler:taler /home/taler/.taler
```

**Крок 4: Увімкнути та запустити сервіс**:

```bash
# Перезавантажити systemd для розпізнавання нового сервісу
sudo systemctl daemon-reload

# Увімкнути сервіс для запуску при завантаженні
sudo systemctl enable talerd

# Запустити сервіс
sudo systemctl start talerd

# Перевірити статус
sudo systemctl status talerd
```

**Корисні команди systemd**:

```bash
# Зупинити сервіс
sudo systemctl stop talerd

# Перезапустити сервіс
sudo systemctl restart talerd

# Переглянути логи
sudo journalctl -u talerd -f

# Вимкнути автозапуск
sudo systemctl disable talerd
```

### Користувацький каталог даних

```bash
talerd -datadir=/mnt/blockchain/taler
```

### Підключення тільки до певних нод

```bash
talerd -connect=192.168.1.100:23153 -connect=192.168.1.101:23153
```

### Увімкнення індексації транзакцій

```bash
talerd -txindex
```

Необхідно для повної функціональності блок-експлорера.

### Переіндексація блокчейну

```bash
talerd -reindex
```

Корисно при пошкодженні бази даних блокчейну.

---

## taler-cli - Інтерфейс командного рядка

`taler-cli` використовується для відправки JSON-RPC команд працюючому екземпляру `talerd`.

### Базове використання

```bash
taler-cli [опції] <команда> [параметри]
```

### Опції підключення

| Опція | Опис | За замовчуванням |
|--------|-------------|---------|
| `-rpcuser=<user>` | Ім'я користувача RPC | - |
| `-rpcpassword=<pw>` | Пароль RPC | - |
| `-rpcport=<port>` | Порт RPC | 23152 |
| `-rpcconnect=<ip>` | Адреса RPC сервера | 127.0.0.1 |
| `-datadir=<dir>` | Каталог даних (для читання conf) | Див. вище |
| `-testnet` | Використовувати testnet | 0 |

### Основні команди

#### Інформація про ноду

```bash
# Отримати загальну інформацію про ноду
taler-cli getinfo

# Отримати інформацію про блокчейн
taler-cli getblockchaininfo

# Отримати інформацію про мережу
taler-cli getnetworkinfo

# Отримати кількість підключень пірів
taler-cli getconnectioncount

# Отримати детальну інформацію про пірів
taler-cli getpeerinfo
```

#### Запити до блокчейну

```bash
# Отримати поточну кількість блоків
taler-cli getblockcount

# Отримати хеш блоку за висотою
taler-cli getblockhash 12345

# Отримати деталі блоку за хешем
taler-cli getblock <blockhash>

# Отримати деталі транзакції
taler-cli gettransaction <txid>

# Отримати сиру транзакцію (hex)
taler-cli getrawtransaction <txid>

# Отримати інформацію про mempool
taler-cli getmempoolinfo

# Отримати транзакції у mempool
taler-cli getrawmempool
```

#### Операції з гаманцем

```bash
# Отримати баланс гаманця
taler-cli getbalance

# Отримати нову адресу для отримання
taler-cli getnewaddress

# Список останніх транзакцій
taler-cli listtransactions

# Список невитрачених виходів
taler-cli listunspent

# Відправити монети
taler-cli sendtoaddress <address> <amount>

# Створити сиру транзакцію
taler-cli createrawtransaction '[{"txid":"...","vout":0}]' '{"address":amount}'

# Підписати сиру транзакцію
taler-cli signrawtransaction <hex>

# Відправити сиру транзакцію
taler-cli sendrawtransaction <hex>
```

#### Команди стейкінгу

```bash
# Отримати статус стейкінгу
taler-cli getstakingstatus

# Отримати інформацію про стейкінг
taler-cli getstakinginfo
```

#### Керування нодою

```bash
# Зупинити демон
taler-cli stop

# Отримати статистику memory pool
taler-cli getmempoolinfo

# Оцінити комісію для транзакції
taler-cli estimatefee <blocks>
```

---

## Файл конфігурації

Замість передачі опцій командного рядка кожного разу, створіть файл конфігурації.

### Розташування

- **Windows**: `%APPDATA%\TALER\taler.conf`
- **macOS**: `~/Taler/taler.conf`
- **Linux**: `~/.taler/taler.conf`

### Приклад конфігурації повної ноди

```ini
# Налаштування мережі
listen=1
port=23153
maxconnections=128

# RPC сервер
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23152
rpcallowip=127.0.0.1

# Продуктивність
dbcache=1024
maxmempool=300
par=-1

# Індексація (опціонально, потрібно для повного блок-експлорера)
txindex=1

# Логування
printtoconsole=0
logtimestamps=1
```

### Приклад конфігурації ноди для майнінгу

```ini
# Мережа
listen=1
maxconnections=64

# RPC
server=1
rpcuser=miner
rpcpassword=mining_password

# Майнінг
gen=1
genproclimit=8

# Продуктивність
dbcache=2048
```

### Приклад конфігурації обрізаної ноди

```ini
# Мережа
listen=1
maxconnections=64

# Обрізка
prune=2000

# RPC
server=1
rpcuser=pruned
rpcpassword=password

# Продуктивність
dbcache=512
```

### Приклад конфігурації для стейкінгу

```ini
# Мережа
listen=1
maxconnections=128

# RPC
server=1
rpcuser=staker
rpcpassword=staking_password

# Стейкінг
stakegen=1
reservebalance=10

# Продуктивність
dbcache=1024
```

---

## Змінні оточення

Ви також можете налаштувати `talerd` і `taler-cli` за допомогою змінних оточення:

```bash
export TALER_DATA_DIR=/custom/path
export TALER_RPC_USER=myuser
export TALER_RPC_PASSWORD=mypassword
```

---

## Моніторинг та обслуговування

### Перевірка прогресу синхронізації

```bash
taler-cli getblockchaininfo | grep "verificationprogress"
```

### Перегляд відладкового логу

**Linux/macOS**:
```bash
tail -f ~/.taler/debug.log
```

**Windows**:
```cmd
type %APPDATA%\TALER\debug.log
```

### Моніторинг мережевих підключень

```bash
watch -n 5 'taler-cli getpeerinfo | grep addr'
```

### Перевірка розміру mempool

```bash
taler-cli getmempoolinfo
```

### Оцінка мережевого хешрейту

```bash
taler-cli getmininginfo
```

---

## Усунення неполадок

### Відмова в підключенні RPC

**Проблема**: `taler-cli` не може підключитися до `talerd`

**Рішення**:
1. Переконайтеся, що `talerd` запущений: `ps aux | grep talerd`
2. Перевірте, що RPC увімкнено в `taler.conf`: `server=1`
3. Переконайтеся, що облікові дані RPC збігаються в `talerd` і `taler-cli`
4. Перевірте, що firewall не блокує порт 23152

### Синхронізація блокчейну застрягла

**Проблема**: Нода припиняє синхронізацію на певній висоті блоку

**Рішення**:
```bash
# Зупинити демон
taler-cli stop

# Переіндексувати блокчейн
talerd -reindex

# Або перебудувати тільки chainstate (швидше)
talerd -reindex-chainstate
```

### Високе використання пам'яті

**Проблема**: `talerd` споживає забагато RAM

**Рішення**:
```bash
# Зменшити кеш бази даних
talerd -dbcache=256

# Зменшити розмір mempool
talerd -maxmempool=100
```

### Гаманець не завантажується

**Проблема**: Гаманець не завантажується при запуску

**Рішення**:
```bash
# Спробувати відновити гаманець
talerd -salvagewallet

# Або оновити формат гаманця
talerd -upgradewallet
```

---

## Просунуте використання

### Запуск декількох нод

Запуск окремих нод mainnet і testnet:

**Mainnet**:
```bash
talerd -datadir=/data/taler-mainnet -port=23153 -rpcport=23152
```

**Testnet**:
```bash
talerd -testnet -datadir=/data/taler-testnet -port=33153 -rpcport=33152
```

### Користувацькі мережеві seeds

Додавання конкретних seed нод для початкового виявлення пірів:

```bash
talerd -seednode=seed1.talercoin.org -seednode=seed2.talercoin.org
```

### Аналіз блокчейну

Увімкнення повної індексації для аналізу:

```bash
talerd -txindex -addressindex -timestampindex -spentindex
```

**Примітка**: Потребує повної переіндексації блокчейну і значно збільшує використання диска.

---

## Кращі практики безпеки

### Безпека RPC

1. **Використовуйте сильні паролі**:
   ```ini
   rpcpassword=$(openssl rand -base64 32)
   ```

2. **Обмежте доступ до RPC**:
   ```ini
   rpcallowip=127.0.0.1
   rpcbind=127.0.0.1
   ```

3. **Ніколи не виставляйте RPC у публічний інтернет** без належної аутентифікації та шифрування

### Права доступу до файлів

Захистіть файл конфігурації:

```bash
chmod 600 ~/.taler/taler.conf
```

### Налаштування firewall

Відкривайте тільки P2P порт (23153), а не RPC порт (23152):

```bash
# Дозволити P2P
sudo ufw allow 23153/tcp

# Заблокувати RPC ззовні
sudo ufw deny 23152/tcp
```

---

## Наступні кроки

- **Розгортання Docker**: Запуск ноди TALER у контейнерах з [Docker](/en/docs/node-docker/)
- **Майнінг і стейкінг**: Дізнайтеся про генерацію блоків у [Майнінг і стейкінг](/en/docs/node-mining/)
- **Довідник RPC**: Повна документація RPC API у [Документація для розробників](/en/docs/rpc-reference/)
