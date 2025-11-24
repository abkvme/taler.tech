---
title: "Командная строка (talerd)"
weight: 10
---

Это руководство содержит полную документацию по инструментам `talerd` (демон TALER) и `taler-cli` (интерфейс командной строки).

## Обзор

TALER предоставляет два основных инструмента командной строки:

- **`talerd`**: Демон блокчейна, который запускает полную ноду
- **`taler-cli`**: Инструмент командной строки для взаимодействия с работающим демоном

---

## talerd - Демон TALER

Демон `talerd` является основным программным обеспечением ноды блокчейна TALER.

### Базовое использование

```bash
talerd [опции]
```

### Основные опции

#### Настройка сети

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-listen` | Принимать входящие соединения | 1 (включено) |
| `-port=<port>` | Слушать на указанном порту | 23153 (mainnet) |
| `-maxconnections=<n>` | Максимальное количество соединений с пирами | 128 |
| `-addnode=<ip>` | Добавить ноду для подключения | - |
| `-connect=<ip>` | Подключаться только к указанной ноде | - |
| `-seednode=<ip>` | Подключиться к ноде для получения начальных пиров | - |
| `-proxy=<ip:port>` | Подключаться через SOCKS5 прокси | - |
| `-bind=<addr>` | Привязаться к конкретному адресу | Все интерфейсы |

#### Управление данными

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-datadir=<dir>` | Указать каталог данных | См. ниже |
| `-prune=<n>` | Включить обрезку (уменьшить использование диска до ~n MB) | 0 (отключено) |
| `-dbcache=<n>` | Размер кэша базы данных в MB | 450 |
| `-maxmempool=<n>` | Максимальный размер mempool в MB | 300 |
| `-blocksdir=<dir>` | Указать каталог для хранения блоков | datadir/blocks |

**Каталоги данных по умолчанию**:
- Windows: `%APPDATA%\TALER\`
- macOS: `~/Taler/`
- Linux: `~/.taler/`

#### RPC сервер

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-server` | Принимать JSON-RPC команды | 0 (отключено) |
| `-rpcuser=<user>` | Имя пользователя RPC | - |
| `-rpcpassword=<pw>` | Пароль RPC | - |
| `-rpcport=<port>` | Слушать RPC на порту | 23152 (mainnet) |
| `-rpcbind=<addr>` | Привязать RPC к адресу | 127.0.0.1 |
| `-rpcallowip=<ip>` | Разрешить RPC с IP (можно использовать несколько раз) | 127.0.0.1 |

#### Майнинг и стейкинг

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-gen` | Генерировать/майнить монеты | 0 (отключено) |
| `-genproclimit=<n>` | Установить потоки майнинга (-1 = все ядра) | -1 |
| `-stakegen` | Включить генерацию стейков (PoS) | 1 (включено) |
| `-reservebalance=<amt>` | Зарезервированная сумма, не используемая для стейкинга | 0 |

#### Блокчейн и синхронизация

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-testnet` | Использовать тестовую сеть | 0 (mainnet) |
| `-reindex` | Перестроить индекс блокчейна из файлов blk*.dat | 0 |
| `-reindex-chainstate` | Перестроить chainstate из текущих блоков | 0 |
| `-txindex` | Поддерживать полный индекс транзакций | 0 |
| `-addressindex` | Поддерживать индекс транзакций на основе адресов | 0 |
| `-timestampindex` | Поддерживать индекс блоков на основе временных меток | 0 |
| `-spentindex` | Поддерживать полный индекс потраченных выходов | 0 |

#### Кошелёк

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-disablewallet` | Не загружать кошелёк | 0 (кошелёк включен) |
| `-wallet=<file>` | Указать файл кошелька | wallet.dat |
| `-zapwallettxes=<mode>` | Удалить все транзакции кошелька (1=сохранить метаданные, 2=удалить) | 0 |
| `-upgradewallet` | Обновить кошелёк до последнего формата | 0 |
| `-salvagewallet` | Попытаться восстановить приватные ключи из поврежденного кошелька | 0 |

#### Логирование и отладка

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-debug=<category>` | Включить отладочное логирование (net, tor, mempool, rpc, bench, db) | 0 |
| `-debuglogfile=<file>` | Указать расположение файла отладочного лога | debug.log |
| `-printtoconsole` | Отправлять информацию трассировки/отладки в консоль | 0 |
| `-logtimestamps` | Добавлять временные метки к отладочному выводу | 1 |
| `-shrinkdebugfile` | Сжимать debug.log при запуске | 1 |

#### Производительность

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-par=<n>` | Установить потоки проверки скриптов (-1 = авто) | -1 |
| `-checkblocks=<n>` | Количество блоков для проверки при запуске | 6 |
| `-checklevel=<n>` | Тщательность проверки блоков (0-4) | 3 |

#### Управление демоном

| Опция | Описание |
|--------|-------------|
| `-daemon` | Запустить в фоновом режиме как демон (только Unix) |
| `-pid=<file>` | Указать расположение PID файла |
| `-conf=<file>` | Указать файл конфигурации |
| `-version` | Вывести информацию о версии |
| `-help` | Показать справочное сообщение |

---

## Примеры использования talerd

### Запуск полной ноды с включенным RPC

```bash
talerd -server -rpcuser=myuser -rpcpassword=mypassword
```

### Запуск обрезанной ноды (экономия места на диске)

```bash
talerd -prune=2000
```

Сохраняет приблизительно 2GB данных блокчейна.

### Запуск в testnet

```bash
talerd -testnet
```

### Включение PoW майнинга

```bash
talerd -gen -genproclimit=4
```

Майнинг с использованием 4 ядер CPU.

### Запуск как фонового демона (Linux/macOS)

```bash
talerd -daemon
```

### Запуск как systemd сервис (Linux)

Для развертывания на продакшн серверах запуск `talerd` как systemd сервиса обеспечивает автоматический запуск, контроль и логирование.

**Шаг 1: Создать файл systemd сервиса**

Создайте `/etc/systemd/system/talerd.service`:

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

**Шаг 2: Создать пользователя taler** (если не существует):

```bash
sudo useradd -r -m -s /bin/bash taler
```

**Шаг 3: Установить права**:

```bash
sudo chown -R taler:taler /home/taler/.taler
```

**Шаг 4: Включить и запустить сервис**:

```bash
# Перезагрузить systemd для распознавания нового сервиса
sudo systemctl daemon-reload

# Включить сервис для запуска при загрузке
sudo systemctl enable talerd

# Запустить сервис
sudo systemctl start talerd

# Проверить статус
sudo systemctl status talerd
```

**Полезные команды systemd**:

```bash
# Остановить сервис
sudo systemctl stop talerd

# Перезапустить сервис
sudo systemctl restart talerd

# Просмотреть логи
sudo journalctl -u talerd -f

# Отключить автозапуск
sudo systemctl disable talerd
```

### Пользовательский каталог данных

```bash
talerd -datadir=/mnt/blockchain/taler
```

### Подключение только к определенным нодам

```bash
talerd -connect=192.168.1.100:23153 -connect=192.168.1.101:23153
```

### Включение индексации транзакций

```bash
talerd -txindex
```

Необходимо для полной функциональности блок-эксплорера.

### Переиндексация блокчейна

```bash
talerd -reindex
```

Полезно при повреждении базы данных блокчейна.

---

## taler-cli - Интерфейс командной строки

`taler-cli` используется для отправки JSON-RPC команд работающему экземпляру `talerd`.

### Базовое использование

```bash
taler-cli [опции] <команда> [параметры]
```

### Опции подключения

| Опция | Описание | По умолчанию |
|--------|-------------|---------|
| `-rpcuser=<user>` | Имя пользователя RPC | - |
| `-rpcpassword=<pw>` | Пароль RPC | - |
| `-rpcport=<port>` | Порт RPC | 23152 |
| `-rpcconnect=<ip>` | Адрес RPC сервера | 127.0.0.1 |
| `-datadir=<dir>` | Каталог данных (для чтения conf) | См. выше |
| `-testnet` | Использовать testnet | 0 |

### Основные команды

#### Информация о ноде

```bash
# Получить общую информацию о ноде
taler-cli getinfo

# Получить информацию о блокчейне
taler-cli getblockchaininfo

# Получить информацию о сети
taler-cli getnetworkinfo

# Получить количество подключений пиров
taler-cli getconnectioncount

# Получить детальную информацию о пирах
taler-cli getpeerinfo
```

#### Запросы к блокчейну

```bash
# Получить текущее количество блоков
taler-cli getblockcount

# Получить хэш блока по высоте
taler-cli getblockhash 12345

# Получить детали блока по хэшу
taler-cli getblock <blockhash>

# Получить детали транзакции
taler-cli gettransaction <txid>

# Получить сырую транзакцию (hex)
taler-cli getrawtransaction <txid>

# Получить информацию о mempool
taler-cli getmempoolinfo

# Получить транзакции в mempool
taler-cli getrawmempool
```

#### Операции с кошельком

```bash
# Получить баланс кошелька
taler-cli getbalance

# Получить новый адрес для получения
taler-cli getnewaddress

# Список последних транзакций
taler-cli listtransactions

# Список неизрасходованных выходов
taler-cli listunspent

# Отправить монеты
taler-cli sendtoaddress <address> <amount>

# Создать сырую транзакцию
taler-cli createrawtransaction '[{"txid":"...","vout":0}]' '{"address":amount}'

# Подписать сырую транзакцию
taler-cli signrawtransaction <hex>

# Отправить сырую транзакцию
taler-cli sendrawtransaction <hex>
```

#### Команды стейкинга

```bash
# Получить статус стейкинга
taler-cli getstakingstatus

# Получить информацию о стейкинге
taler-cli getstakinginfo
```

#### Управление нодой

```bash
# Остановить демон
taler-cli stop

# Получить статистику memory pool
taler-cli getmempoolinfo

# Оценить комиссию для транзакции
taler-cli estimatefee <blocks>
```

---

## Файл конфигурации

Вместо передачи опций командной строки каждый раз, создайте файл конфигурации.

### Расположение

- **Windows**: `%APPDATA%\TALER\taler.conf`
- **macOS**: `~/Taler/taler.conf`
- **Linux**: `~/.taler/taler.conf`

### Пример конфигурации полной ноды

```ini
# Настройки сети
listen=1
port=23153
maxconnections=128

# RPC сервер
server=1
rpcuser=yourusername
rpcpassword=your_strong_password_here
rpcport=23152
rpcallowip=127.0.0.1

# Производительность
dbcache=1024
maxmempool=300
par=-1

# Индексация (опционально, требуется для полного блок-эксплорера)
txindex=1

# Логирование
printtoconsole=0
logtimestamps=1
```

### Пример конфигурации ноды для майнинга

```ini
# Сеть
listen=1
maxconnections=64

# RPC
server=1
rpcuser=miner
rpcpassword=mining_password

# Майнинг
gen=1
genproclimit=8

# Производительность
dbcache=2048
```

### Пример конфигурации обрезанной ноды

```ini
# Сеть
listen=1
maxconnections=64

# Обрезка
prune=2000

# RPC
server=1
rpcuser=pruned
rpcpassword=password

# Производительность
dbcache=512
```

### Пример конфигурации для стейкинга

```ini
# Сеть
listen=1
maxconnections=128

# RPC
server=1
rpcuser=staker
rpcpassword=staking_password

# Стейкинг
stakegen=1
reservebalance=10

# Производительность
dbcache=1024
```

---

## Переменные окружения

Вы также можете настроить `talerd` и `taler-cli` с помощью переменных окружения:

```bash
export TALER_DATA_DIR=/custom/path
export TALER_RPC_USER=myuser
export TALER_RPC_PASSWORD=mypassword
```

---

## Мониторинг и обслуживание

### Проверка прогресса синхронизации

```bash
taler-cli getblockchaininfo | grep "verificationprogress"
```

### Просмотр отладочного лога

**Linux/macOS**:
```bash
tail -f ~/.taler/debug.log
```

**Windows**:
```cmd
type %APPDATA%\TALER\debug.log
```

### Мониторинг сетевых подключений

```bash
watch -n 5 'taler-cli getpeerinfo | grep addr'
```

### Проверка размера mempool

```bash
taler-cli getmempoolinfo
```

### Оценка сетевого хешрейта

```bash
taler-cli getmininginfo
```

---

## Устранение неполадок

### Отказ в подключении RPC

**Проблема**: `taler-cli` не может подключиться к `talerd`

**Решения**:
1. Убедитесь, что `talerd` запущен: `ps aux | grep talerd`
2. Проверьте, что RPC включен в `taler.conf`: `server=1`
3. Убедитесь, что учетные данные RPC совпадают в `talerd` и `taler-cli`
4. Проверьте, что firewall не блокирует порт 23152

### Синхронизация блокчейна застряла

**Проблема**: Нода прекращает синхронизацию на определенной высоте блока

**Решения**:
```bash
# Остановить демон
taler-cli stop

# Переиндексировать блокчейн
talerd -reindex

# Или перестроить только chainstate (быстрее)
talerd -reindex-chainstate
```

### Высокое использование памяти

**Проблема**: `talerd` потребляет слишком много RAM

**Решения**:
```bash
# Уменьшить кэш базы данных
talerd -dbcache=256

# Уменьшить размер mempool
talerd -maxmempool=100
```

### Кошелёк не загружается

**Проблема**: Кошелёк не загружается при запуске

**Решения**:
```bash
# Попытаться восстановить кошелёк
talerd -salvagewallet

# Или обновить формат кошелька
talerd -upgradewallet
```

---

## Продвинутое использование

### Запуск нескольких нод

Запуск отдельных нод mainnet и testnet:

**Mainnet**:
```bash
talerd -datadir=/data/taler-mainnet -port=23153 -rpcport=23152
```

**Testnet**:
```bash
talerd -testnet -datadir=/data/taler-testnet -port=33153 -rpcport=33152
```

### Пользовательские сетевые seeds

Добавление конкретных seed нод для начального обнаружения пиров:

```bash
talerd -seednode=seed1.talercoin.org -seednode=seed2.talercoin.org
```

### Анализ блокчейна

Включение полной индексации для анализа:

```bash
talerd -txindex -addressindex -timestampindex -spentindex
```

**Примечание**: Требует полной переиндексации блокчейна и значительно увеличивает использование диска.

---

## Лучшие практики безопасности

### Безопасность RPC

1. **Используйте сильные пароли**:
   ```ini
   rpcpassword=$(openssl rand -base64 32)
   ```

2. **Ограничьте доступ к RPC**:
   ```ini
   rpcallowip=127.0.0.1
   rpcbind=127.0.0.1
   ```

3. **Никогда не выставляйте RPC в публичный интернет** без надлежащей аутентификации и шифрования

### Права доступа к файлам

Защитите файл конфигурации:

```bash
chmod 600 ~/.taler/taler.conf
```

### Настройка firewall

Открывайте только P2P порт (23153), а не RPC порт (23152):

```bash
# Разрешить P2P
sudo ufw allow 23153/tcp

# Заблокировать RPC извне
sudo ufw deny 23152/tcp
```

---

## Следующие шаги

- **Развертывание Docker**: Запуск ноды TALER в контейнерах с [Docker](/en/docs/node-docker/)
- **Майнинг и стейкинг**: Узнайте о генерации блоков в [Майнинг и стейкинг](/en/docs/node-mining/)
- **Справочник RPC**: Полная документация RPC API в [Документация для разработчиков](/en/docs/rpc-reference/)
