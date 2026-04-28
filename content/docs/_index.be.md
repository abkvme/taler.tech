---
title: "Дакументацыя"
description: "Поўная дакументацыя па блакчэйне TALER - кіраўніцтвы для карыстальнікаў, распрацоўшчыкаў і валідатараў"
---

Вітаем у дакументацыі блакчэйна TALER. Тут вы знойдзеце зыходныя кіраўніцтвы і даведнікі па ўсіх аспектах платформы.

## Зыходны код і рэпазіторыі

Экасістэма TALER цалкам адкрытая. Усе праекты на GitHub:

| Праект | Рэпазіторый | Прызначэнне |
|---|---|---|
| **Taler Core** | [github.com/abkvme/taler](https://github.com/abkvme/taler) | Вузел сеткі (`talerd`), CLI (`taler-cli`) і GUI-кашалёк (`taler-qt`). Актуальныя рэлізы для Linux, macOS і Windows. |
| **Taler Explorer** | [github.com/abkvme/taler-explorer](https://github.com/abkvme/taler-explorer) | Аглядальнік блакчэйна, на якім працуе [explorer.taler.tech](https://explorer.taler.tech/). Аднафайлавы Go-сэрвіс, які размаўляе з `talerd` па JSON-RPC. |
| **taler.tech** | [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech) | Гэты сайт дакументацыі (Hugo). |
| **Legacy Core** | [github.com/cryptadev/taler](https://github.com/cryptadev/taler) | Гістарычныя зыходнікі Taler да v0.17.2.6. Для ўсяго новага выкарыстоўвайце Taler Core вышэй. |

Гатовыя кантэйнерныя выявы апублікаваныя ў GitHub Container Registry:

```
docker pull ghcr.io/abkvme/taler:latest             # Вузел Taler
docker pull ghcr.io/abkvme/taler-explorer:latest    # Аглядальнік
```

## Дапамога ў развіцці дакументацыі

Гэтая дакументацыя падтрымліваецца супольнасцю і адкрыта для ўкладу. Вы можаце дапамагчы палепшыць яе:

- Выпраўляючы друкарскія памылкі або нязрозумелыя тлумачэнні
- Дадаючы адсутную інфармацыю
- Паляпшаючы пераклады
- Абнаўляючы састарэлы кантэнт

**Як зрабіць свой уклад:**

1. Наведайце рэпазіторый сайта: [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech)
2. Зрабіце форк рэпазіторыя і ўнясіце змены
3. Адпраўце pull request з вашымі паляпшэннямі

Усе файлы дакументацыі знаходзяцца ў `/content/docs/` з версіямі на англійскай, рускай, беларускай і ўкраінскай мовах. Падрабязныя інструкцыі па ўнясенні ўкладу гл. у README рэпазіторыя.
