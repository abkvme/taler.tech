---
title: "Камандны радок (talerd)"
weight: 10
---

Гэта кіраўніцтва змяшчае поўную дакументацыю па інструментах `talerd` (дэман TALER) і `taler-cli` (інтэрфейс каманднага радка).

## Агляд

TALER прадастаўляе два асноўныя інструменты каманднага радка:

- **`talerd`**: Дэман блокчэйна, які запускае поўную ноду
- **`taler-cli`**: Інструмент каманднага радка для ўзаемадзеяння з працуючым дэманам

---

## talerd - Дэман TALER

Дэман `talerd` з'яўляецца асноўным праграмным забеспячэннем ноды блокчэйна TALER.

### Базавае выкарыстанне

```bash
talerd [опцыі]
```

### Асноўныя опцыі

#### Налада сеткі

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-listen` | Прымаць уваходныя злучэнні | 1 (уключана) |
| `-port=<port>` | Слухаць на паказаным порце | 23153 (mainnet) |
| `-maxconnections=<n>` | Максімальная колькасць злучэнняў з пірамі | 128 |
| `-addnode=<ip>` | Дадаць ноду для падключэння | - |
| `-connect=<ip>` | Падключацца толькі да паказанай ноды | - |
| `-seednode=<ip>` | Падключыцца да ноды для атрымання пачатковых піраў | - |
| `-proxy=<ip:port>` | Падключацца праз SOCKS5 проксі | - |
| `-bind=<addr>` | Прывязацца да канкрэтнага адраса | Усе інтэрфейсы |

#### Кіраванне дадзенымі

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-datadir=<dir>` | Паказаць каталог дадзеных | Гл. ніжэй |
| `-prune=<n>` | Уключыць абрэзку (паменшыць выкарыстанне дыска да ~n MB) | 0 (адключана) |
| `-dbcache=<n>` | Памер кэша базы дадзеных у MB | 450 |
| `-maxmempool=<n>` | Максімальны памер mempool у MB | 300 |
| `-blocksdir=<dir>` | Паказаць каталог для захавання блокаў | datadir/blocks |

**Каталогі дадзеных па змаўчанні**:
- Windows: `%APPDATA%\TALER\`
- macOS: `~/Taler/`
- Linux: `~/.taler/`

#### RPC сервер

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-server` | Прымаць JSON-RPC каманды | 0 (адключана) |
| `-rpcuser=<user>` | Імя карыстальніка RPC | - |
| `-rpcpassword=<pw>` | Пароль RPC | - |
| `-rpcport=<port>` | Слухаць RPC на порце | 23152 (mainnet) |
| `-rpcbind=<addr>` | Прывязаць RPC да адраса | 127.0.0.1 |
| `-rpcallowip=<ip>` | Дазволіць RPC з IP (можна выкарыстоўваць некалькі разоў) | 127.0.0.1 |

#### Майнінг і стэйкінг

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-gen` | Генераваць/майніць манеты | 0 (адключана) |
| `-genproclimit=<n>` | Усталяваць патокі майнінгу (-1 = усе ядры) | -1 |
| `-stakegen` | Уключыць генерацыю стэйкаў (PoS) | 1 (уключана) |
| `-reservebalance=<amt>` | Зарэзерваваная сума, не выкарыстоўваная для стэйкінгу | 0 |

#### Блокчэйн і сінхранізацыя

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-testnet` | Выкарыстоўваць тэставую сетку | 0 (mainnet) |
| `-reindex` | Перабудаваць індэкс блокчэйна з файлаў blk*.dat | 0 |
| `-reindex-chainstate` | Перабудаваць chainstate з бягучых блокаў | 0 |
| `-txindex` | Падтрымліваць поўны індэкс транзакцый | 0 |
| `-addressindex` | Падтрымліваць індэкс транзакцый на аснове адрасоў | 0 |
| `-timestampindex` | Падтрымліваць індэкс блокаў на аснове часавых адзнак | 0 |
| `-spentindex` | Падтрымліваць поўны індэкс выдаткаваных выхадаў | 0 |

#### Кашалёк

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-disablewallet` | Не загружаць кашалёк | 0 (кашалёк уключаны) |
| `-wallet=<file>` | Паказаць файл кашалька | wallet.dat |
| `-zapwallettxes=<mode>` | Выдаліць усе транзакцыі кашалька (1=захаваць метададзеныя, 2=выдаліць) | 0 |
| `-upgradewallet` | Абнавіць кашалёк да апошняга фармату | 0 |
| `-salvagewallet` | Паспрабаваць аднавіць прыватныя ключы з пашкоджанага кашалька | 0 |

#### Лагіраванне і адладка

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-debug=<category>` | Уключыць адладачнае лагіраванне (net, tor, mempool, rpc, bench, db) | 0 |
| `-debuglogfile=<file>` | Паказаць размяшчэнне файла адладачнага лога | debug.log |
| `-printtoconsole` | Адпраўляць інфармацыю трасіроўкі/адладкі ў кансоль | 0 |
| `-logtimestamps` | Дадаваць часавыя адзнакі да адладачнага вываду | 1 |
| `-shrinkdebugfile` | Сціскаць debug.log пры запуску | 1 |

#### Прадукцыйнасць

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-par=<n>` | Усталяваць патокі праверкі скрыптоў (-1 = аўта) | -1 |
| `-checkblocks=<n>` | Колькасць блокаў для праверкі пры запуску | 6 |
| `-checklevel=<n>` | Дбайнасць праверкі блокаў (0-4) | 3 |

#### Кіраванне дэманам

| Опцыя | Апісанне |
|--------|-------------|
| `-daemon` | Запусціць у фонавым рэжыме як дэман (толькі Unix) |
| `-pid=<file>` | Паказаць размяшчэнне PID файла |
| `-conf=<file>` | Паказаць файл канфігурацыі |
| `-version` | Вывесці інфармацыю аб версіі |
| `-help` | Паказаць даведачнае паведамленне |

---

## Прыклады выкарыстання talerd

### Запуск поўнай ноды з уключаным RPC

```bash
talerd -server -rpcuser=myuser -rpcpassword=mypassword
```

### Запуск абрэзанай ноды (эканомія месца на дыску)

```bash
talerd -prune=2000
```

Захоўвае прыблізна 2GB дадзеных блокчэйна.

### Запуск у testnet

```bash
talerd -testnet
```

### Уключэнне PoW майнінгу

```bash
talerd -gen -genproclimit=4
```

Майнінг з выкарыстаннем 4 ядраў CPU.

### Запуск як фонавага дэмана (Linux/macOS)

```bash
talerd -daemon
```

### Запуск як systemd сэрвіс (Linux)

Для разгортвання на прадакшн серверах запуск `talerd` як systemd сэрвісу забяспечвае аўтаматычны запуск, кантроль і лагіраванне.

**Крок 1: Стварыць файл systemd сэрвісу**

Стварыце `/etc/systemd/system/talerd.service`:

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

**Крок 2: Стварыць карыстальніка taler** (калі не існує):

```bash
sudo useradd -r -m -s /bin/bash taler
```

**Крок 3: Усталяваць правы**:

```bash
sudo chown -R taler:taler /home/taler/.taler
```

**Крок 4: Уключыць і запусціць сэрвіс**:

```bash
# Перазагрузіць systemd для распазнавання новага сэрвісу
sudo systemctl daemon-reload

# Уключыць сэрвіс для запуску пры загрузцы
sudo systemctl enable talerd

# Запусціць сэрвіс
sudo systemctl start talerd

# Праверыць статус
sudo systemctl status talerd
```

**Карысныя каманды systemd**:

```bash
# Спыніць сэрвіс
sudo systemctl stop talerd

# Перазапусціць сэрвіс
sudo systemctl restart talerd

# Прагледзець логі
sudo journalctl -u talerd -f

# Адключыць аўтазапуск
sudo systemctl disable talerd
```

### Карыстальніцкі каталог дадзеных

```bash
talerd -datadir=/mnt/blockchain/taler
```

### Падключэнне толькі да пэўных нодаў

```bash
talerd -connect=192.168.1.100:23153 -connect=192.168.1.101:23153
```

### Уключэнне індэксацыі транзакцый

```bash
talerd -txindex
```

Неабходна для поўнай функцыянальнасці блок-эксплорера.

### Пераіндэксацыя блокчэйна

```bash
talerd -reindex
```

Карысна пры пашкоджанні базы дадзеных блокчэйна.

---

## taler-cli - Інтэрфейс каманднага радка

`taler-cli` выкарыстоўваецца для адпраўкі JSON-RPC каманд працуючаму асобніку `talerd`.

### Базавае выкарыстанне

```bash
taler-cli [опцыі] <каманда> [параметры]
```

### Опцыі падключэння

| Опцыя | Апісанне | Па змаўчанні |
|--------|-------------|---------|
| `-rpcuser=<user>` | Імя карыстальніка RPC | - |
| `-rpcpassword=<pw>` | Пароль RPC | - |
| `-rpcport=<port>` | Порт RPC | 23152 |
| `-rpcconnect=<ip>` | Адрас RPC сервера | 127.0.0.1 |
| `-datadir=<dir>` | Каталог дадзеных (для чытання conf) | Гл. вышэй |
| `-testnet` | Выкарыстоўваць testnet | 0 |

### Асноўныя каманды

#### Інфармацыя аб нодзе

```bash
# Атрымаць агульную інфармацыю аб нодзе
taler-cli getinfo

# Атрымаць інфармацыю аб блокчэйне
taler-cli getblockchaininfo

# Атрымаць інфармацыю аб сетцы
taler-cli getnetworkinfo

# Атрымаць колькасць падключэнняў піраў
taler-cli getconnectioncount

# Атрымаць дэтальную інфармацыю аб пірах
taler-cli getpeerinfo
```

#### Запыты да блокчэйна

```bash
# Атрымаць бягучую колькасць блокаў
taler-cli getblockcount

# Атрымаць хэш блока па вышыні
taler-cli getblockhash 12345

# Атрымаць дэталі блока па хэшу
taler-cli getblock <blockhash>

# Атрымаць дэталі транзакцыі
taler-cli gettransaction <txid>

# Атрымаць сырую транзакцыю (hex)
taler-cli getrawtransaction <txid>

# Атрымаць інфармацыю аб mempool
taler-cli getmempoolinfo

# Атрымаць транзакцыі ў mempool
taler-cli getrawmempool
```

#### Аперацыі з кашальком

```bash
# Атрымаць баланс кашалька
taler-cli getbalance

# Атрымаць новы адрас для атрымання
taler-cli getnewaddress

# Спіс апошніх транзакцый
taler-cli listtransactions

# Спіс невыдаткаваных выхадаў
taler-cli listunspent

# Адправіць манеты
taler-cli sendtoaddress <address> <amount>

# Стварыць сырую транзакцыю
taler-cli createrawtransaction '[{"txid":"...","vout":0}]' '{"address":amount}'

# Падпісаць сырую транзакцыю
taler-cli signrawtransaction <hex>

# Адправіць сырую транзакцыю
taler-cli sendrawtransaction <hex>
```

#### Каманды стэйкінгу

```bash
# Атрымаць статус стэйкінгу
taler-cli getstakingstatus

# Атрымаць інфармацыю аб стэйкінгу
taler-cli getstakinginfo
```

#### Кіраванне нодай

```bash
# Спыніць дэман
taler-cli stop

# Атрымаць статыстыку memory pool
taler-cli getmempoolinfo

# Ацаніць камісію для транзакцыі
taler-cli estimatefee <blocks>
```

---

## Файл канфігурацыі

Замест перадачы опцый каманднага радка кожны раз, стварыце файл канфігурацыі.

### Размяшчэнне

- **Windows**: `%APPDATA%\TALER\taler.conf`
- **macOS**: `~/Taler/taler.conf`
- **Linux**: `~/.taler/taler.conf`

### Прыклад канфігурацыі поўнай ноды

```ini
# Налады сеткі
listen=1
port=23153
maxconnections=128

# RPC сервер
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23152
rpcallowip=127.0.0.1

# Прадукцыйнасць
dbcache=1024
maxmempool=300
par=-1

# Індэксацыя (апцыянальна, патрабуецца для поўнага блок-эксплорера)
txindex=1

# Лагіраванне
printtoconsole=0
logtimestamps=1
```

### Прыклад канфігурацыі ноды для майнінгу

```ini
# Сетка
listen=1
maxconnections=64

# RPC
server=1
rpcuser=miner
rpcpassword=mining_password

# Майнінг
gen=1
genproclimit=8

# Прадукцыйнасць
dbcache=2048
```

### Прыклад канфігурацыі абрэзанай ноды

```ini
# Сетка
listen=1
maxconnections=64

# Абрэзка
prune=2000

# RPC
server=1
rpcuser=pruned
rpcpassword=password

# Прадукцыйнасць
dbcache=512
```

### Прыклад канфігурацыі для стэйкінгу

```ini
# Сетка
listen=1
maxconnections=128

# RPC
server=1
rpcuser=staker
rpcpassword=staking_password

# Стэйкінг
stakegen=1
reservebalance=10

# Прадукцыйнасць
dbcache=1024
```

---

## Пераменныя асяроддзя

Вы таксама можаце наладзіць `talerd` і `taler-cli` з дапамогай пераменных асяроддзя:

```bash
export TALER_DATA_DIR=/custom/path
export TALER_RPC_USER=myuser
export TALER_RPC_PASSWORD=mypassword
```

---

## Маніторынг і абслугоўванне

### Праверка прагрэсу сінхранізацыі

```bash
taler-cli getblockchaininfo | grep "verificationprogress"
```

### Прагляд адладачнага лога

**Linux/macOS**:
```bash
tail -f ~/.taler/debug.log
```

**Windows**:
```cmd
type %APPDATA%\TALER\debug.log
```

### Маніторынг сеткавых падключэнняў

```bash
watch -n 5 'taler-cli getpeerinfo | grep addr'
```

### Праверка памеру mempool

```bash
taler-cli getmempoolinfo
```

### Ацэнка сеткавага хэшрэйту

```bash
taler-cli getmininginfo
```

---

## Ліквідацыя непаладак

### Адмова ў падключэнні RPC

**Праблема**: `taler-cli` не можа падключыцца да `talerd`

**Рашэнні**:
1. Пераканайцеся, што `talerd` запушчаны: `ps aux | grep talerd`
2. Праверце, што RPC уключаны ў `taler.conf`: `server=1`
3. Пераканайцеся, што ўліковыя дадзеныя RPC супадаюць у `talerd` і `taler-cli`
4. Праверце, што firewall не блакуе порт 23152

### Сінхранізацыя блокчэйна застрыла

**Праблема**: Нода спыняе сінхранізацыю на пэўнай вышыні блока

**Рашэнні**:
```bash
# Спыніць дэман
taler-cli stop

# Пераіндэксаваць блокчэйн
talerd -reindex

# Альбо перабудаваць толькі chainstate (хутчэй)
talerd -reindex-chainstate
```

### Высокае выкарыстанне памяці

**Праблема**: `talerd` спажывае занадта шмат RAM

**Рашэнні**:
```bash
# Паменшыць кэш базы дадзеных
talerd -dbcache=256

# Паменшыць памер mempool
talerd -maxmempool=100
```

### Кашалёк не загружаецца

**Праблема**: Кашалёк не загружаецца пры запуску

**Рашэнні**:
```bash
# Паспрабаваць аднавіць кашалёк
talerd -salvagewallet

# Альбо абнавіць фармат кашалька
talerd -upgradewallet
```

---

## Прасунутае выкарыстанне

### Запуск некалькіх нодаў

Запуск асобных нодаў mainnet і testnet:

**Mainnet**:
```bash
talerd -datadir=/data/taler-mainnet -port=23153 -rpcport=23152
```

**Testnet**:
```bash
talerd -testnet -datadir=/data/taler-testnet -port=33153 -rpcport=33152
```

### Карыстальніцкія сеткавыя seeds

Даданне канкрэтных seed нодаў для пачатковага выяўлення піраў:

```bash
talerd -seednode=seed1.talercoin.org -seednode=seed2.talercoin.org
```

### Аналіз блокчэйна

Уключэнне поўнай індэксацыі для аналізу:

```bash
talerd -txindex -addressindex -timestampindex -spentindex
```

**Заўвага**: Патрабуе поўнай пераіндэксацыі блокчэйна і значна павялічвае выкарыстанне дыска.

---

## Лепшыя практыкі бяспекі

### Бяспека RPC

1. **Выкарыстоўвайце моцныя паролі**:
   ```ini
   rpcpassword=$(openssl rand -base64 32)
   ```

2. **Абмежуйце доступ да RPC**:
   ```ini
   rpcallowip=127.0.0.1
   rpcbind=127.0.0.1
   ```

3. **Ніколі не выстаўляйце RPC у публічны інтэрнэт** без належнай аўтэнтыфікацыі і шыфравання

### Правы доступу да файлаў

Абараніце файл канфігурацыі:

```bash
chmod 600 ~/.taler/taler.conf
```

### Налада firewall

Адкрывайце толькі P2P порт (23153), а не RPC порт (23152):

```bash
# Дазволіць P2P
sudo ufw allow 23153/tcp

# Заблакаваць RPC звонку
sudo ufw deny 23152/tcp
```

---

## Наступныя крокі

- **Разгортванне Docker**: Запуск ноды TALER у кантэйнерах з [Docker](/en/docs/node-docker/)
- **Майнінг і стэйкінг**: Даведайцеся аб генерацыі блокаў у [Майнінг і стэйкінг](/en/docs/node-mining/)
- **Даведнік RPC**: Поўная дакументацыя RPC API ў [Дакументацыя для распрацоўшчыкаў](/en/docs/rpc-reference/)
