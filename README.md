# Deep Bodycamp — оплата Lava.top + PuzzleBot

Оплата Deep Bodycamp через Lava.top из PuzzleBot с записью в Google Таблицу «deep body camp регистрации».

- `deepbodycamp.gs` — скрипт Apps Script в таблице.
- `docs/index.html` — страница оплаты (GitHub Pages): https://deepmindcons.github.io/deepbodycamp/

## Как это работает

1. В боте человек оставляет почту и нажимает кнопку тарифа.
2. Кнопка открывает страницу оплаты — там выбор валюты и промокод.
3. Страница просит скрипт создать счёт в Lava на почту человека и переводит на оплату.
4. После оплаты Lava возвращает на страницу «Оплата прошла успешно».
5. Скрипт пишет оплату на лист «Lava.top» (с тарифом) и запускает в боте команду выдачи доступа:
   2300 → `COMMAND_NO_REC`, 3300 и 4300 → `COMMAND_REC`.
6. На листах «Без записей 2300», «С записями 3300», «С записями 4300» в столбце E «Оплата» ставит:
   «Успешно» — оплачен этот тариф, «Оплачен другой тариф: …», «Нет оплаты».
   Раз в 5 минут скрипт сам перепроверяет неоплаченные счета.

## Настройка

1. Таблица → Расширения → Apps Script → вставить `deepbodycamp.gs`.
2. Вверху скрипта вписать `LAVA_API_KEY`, `PUZZLEBOT_TOKEN` и названия команд бота
   `COMMAND_NO_REC` (2300) и `COMMAND_REC` (3300, 4300).
   Ключ, токен и названия команд вписываются только в Apps Script — в GitHub их не храним.
   ID цен уже вписаны (продукт Lava `ada2eaaa-a138-4b80-abaf-690d6449615f`).
3. Развернуть как веб-приложение (запуск от моего имени, доступ — все), адрес `/exec`
   вписать в `SCRIPT_URL` в `docs/index.html`.
4. Меню «Lava» → «1. Включить автопроверку».

## Ссылки для кнопок в боте

| Тариф | Ссылка |
|---|---|
| 2300 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{email_camp2300}}&offer=1a49501b-ff80-40da-8841-de64f310d9b4` |
| 3300 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{email_camp3300}}&offer=5ba3f4cf-ebd4-428f-b31e-c4ef11acda4a` |
| 4300 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{email_camp4300}}&offer=111ca105-a35c-457a-a2d0-f2fa543f413e` |

Проверка оплаты из бота (ответ `paid` / `unpaid`), например для 2300:

```
https://script.google.com/macros/s/AKfycby5bCIbi2CQ4u0z3oeZu0v41c6DdKlhLL_v5SAQE3gINap9evvCjxEX-icPbGEl61jZ/exec?api=check&email={{email_camp2300}}&offer=1a49501b-ff80-40da-8841-de64f310d9b4&format=text
```

Без `&offer=…` — проверка оплаты любого тарифа.

## Важно

- API-ключ Lava, токен PuzzleBot и названия команд хранятся **только** в Apps Script.
- После правок `deepbodycamp.gs` вставьте код в Apps Script и выпустите новую версию развёртывания
  (Развертывание → Управление развертываниями → карандаш → Новая версия), чтобы адрес `/exec` не поменялся.
