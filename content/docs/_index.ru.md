---
title: "Документация"
description: "Полная документация по блокчейну TALER - руководства для пользователей, разработчиков и валидаторов"
---

Добро пожаловать в документацию блокчейна TALER Blockchain. Здесь вы найдете исчерпывающие руководства и справочники по всем аспектам платформы.

## Исходный код и репозитории

Экосистема TALER полностью открыта. Все проекты находятся на GitHub:

| Проект | Репозиторий | Назначение |
|---|---|---|
| **Taler Core** | [github.com/abkvme/taler](https://github.com/abkvme/taler) | Узел сети (`talerd`), CLI (`taler-cli`) и GUI-кошелёк (`taler-qt`). Актуальные релизы для Linux, macOS и Windows. |
| **Taler Explorer** | [github.com/abkvme/taler-explorer](https://github.com/abkvme/taler-explorer) | Обозреватель блокчейна, на котором работает [explorer.taler.tech](https://explorer.taler.tech/). Однофайловый Go-сервис, общающийся с `talerd` по JSON-RPC. |
| **taler.tech** | [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech) | Этот сайт документации (Hugo). |
| **Taler Spec** | [github.com/abkvme/taler.spec](https://github.com/abkvme/taler.spec) | Спецификация деривации ключей кошелька (BIP39 / BIP32 / BIP44, тип монеты SLIP-44 1524) и общие тест-векторы, по которым проверяется каждая реализация кошелька Taler. |
| **Legacy Core** | [github.com/cryptadev/taler](https://github.com/cryptadev/taler) | Исторические исходники Taler до v0.17.2.6. Для всего нового используйте Taler Core выше. |

Готовые контейнерные образы опубликованы в GitHub Container Registry:

```
docker pull ghcr.io/abkvme/taler:latest             # Узел Taler
docker pull ghcr.io/abkvme/taler-explorer:latest    # Обозреватель
```

## Помощь в развитии документации

Эта документация поддерживается сообществом и открыта для вклада. Вы можете помочь улучшить её:

- Исправляя опечатки или неясные объяснения
- Добавляя недостающую информацию
- Улучшая переводы
- Обновляя устаревший контент

**Как внести вклад:**

1. Посетите репозиторий сайта: [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech)
2. Сделайте форк репозитория и внесите изменения
3. Отправьте pull request с вашими улучшениями

Все файлы документации находятся в `/content/docs/` с версиями на английском, русском, белорусском и украинском языках. Подробные инструкции по внесению вклада см. в README репозитория.
