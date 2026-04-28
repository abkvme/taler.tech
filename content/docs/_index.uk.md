---
title: "Документація"
description: "Повна документація з блокчейну TALER - посібники для користувачів, розробників та валідаторів"
---

Ласкаво просимо до документації блокчейну TALER. Тут ви знайдете вичерпні посібники та довідники з усіх аспектів платформи.

## Вихідний код та репозиторії

Екосистема TALER повністю відкрита. Усі проєкти на GitHub:

| Проєкт | Репозиторій | Призначення |
|---|---|---|
| **Taler Core** | [github.com/abkvme/taler](https://github.com/abkvme/taler) | Вузол мережі (`talerd`), CLI (`taler-cli`) та GUI-гаманець (`taler-qt`). Актуальні релізи для Linux, macOS та Windows. |
| **Taler Explorer** | [github.com/abkvme/taler-explorer](https://github.com/abkvme/taler-explorer) | Оглядач блокчейну, на якому працює [explorer.taler.tech](https://explorer.taler.tech/). Однофайловий Go-сервіс, що спілкується з `talerd` через JSON-RPC. |
| **taler.tech** | [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech) | Цей сайт документації (Hugo). |
| **Legacy Core** | [github.com/cryptadev/taler](https://github.com/cryptadev/taler) | Історичні джерела Taler до v0.17.2.6. Для всього нового використовуйте Taler Core вище. |

Готові контейнерні образи опубліковано в GitHub Container Registry:

```
docker pull ghcr.io/abkvme/taler:latest             # Вузол Taler
docker pull ghcr.io/abkvme/taler-explorer:latest    # Оглядач
```

## Допомога у розвитку документації

Ця документація підтримується спільнотою та відкрита для внесків. Ви можете допомогти покращити її:

- Виправляючи друкарські помилки або незрозумілі пояснення
- Додаючи відсутню інформацію
- Покращуючи переклади
- Оновлюючи застарілий контент

**Як зробити внесок:**

1. Відвідайте репозиторій сайту: [github.com/abkvme/taler.tech](https://github.com/abkvme/taler.tech)
2. Зробіть форк репозиторія та внесіть зміни
3. Надішліть pull request з вашими покращеннями

Усі файли документації знаходяться у `/content/docs/` з версіями англійською, російською, білоруською та українською мовами. Детальні інструкції щодо внесення вкладу див. у README репозиторія.
