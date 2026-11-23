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
   2900 → `COMMAND_NO_REC`, 3900, 4900 и 5900 → `COMMAND_REC`.
6. На листах «Без записей» (2900) и «С записями» (3900, 4900, 5900) в столбце E «Оплата» ставит:
   «Успешно» — оплачен тариф этого листа, «Оплачен другой тариф: …», «Нет оплаты».
7. Лист «Итоги» считается формулами таблицы, скрипт его не трогает.
   Раз в 5 минут скрипт сам перепроверяет неоплаченные счета.

## Настройка

1. Таблица → Расширения → Apps Script → вставить `deepbodycamp.gs`.
2. Вверху скрипта вписать `LAVA_API_KEY`, `PUZZLEBOT_TOKEN` и названия команд бота
   `COMMAND_NO_REC` (2300) и `COMMAND_REC` (3300, 4300).
   Ключ, токен и названия команд вписываются только в Apps Script — в GitHub их не храним.
   ID цен четырёх тарифов вписаны в `TARIFFS` (продукт Lava `ada2eaaa-a138-4b80-abaf-690d6449615f`).
3. Развернуть как веб-приложение (запуск от моего имени, доступ — все), адрес `/exec`
   вписать в `SCRIPT_URL` в `docs/index.html`.
4. Меню «Lava» → «1. Включить автопроверку».
5. Вебхук Lava (чтобы оплаты приходили сразу): в Lava → Интеграции → Webhooks добавить адрес
   `…/exec?key=SECRET` (SECRET — строка из скрипта), события «Успешная оплата» и «Возврат».
   Проверка: после оплаты на листе «Лог» появляется строка `POST` со статусом `saved`.

## Нагрузка

- Счета в Lava создаются без очереди: скрипт блокирует таблицу только на запись строки.
- Неоплаченные счета младше часа проверяются каждые 5 минут, старше — раз в 3 часа,
  пачками по 20 запросов одновременно. Через 3 дня неоплаченный счёт помечается истёкшим.
- С вебхуком оплаты приходят сразу, а проверки по расписанию остаются страховкой.
- Вебхук принимает только оплаты счетов из бота и продукта Deep Body Camp, остальные продукты аккаунта пропускает.

## Ссылки для кнопок в боте

| Тариф | Ссылка |
|---|---|
| 2900 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{email_camp_norecs}}&offer=1a49501b-ff80-40da-8841-de64f310d9b4` |
| 3900 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{email_camp_wrecs}}&offer=5ba3f4cf-ebd4-428f-b31e-c4ef11acda4a` |
| 4900 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{email_camp_wrecs}}&offer=111ca105-a35c-457a-a2d0-f2fa543f413e` |
| 5900 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{email_camp_wrecs}}&offer=87a95f6d-e01c-4ea6-9218-b06becbccd1a` |

Проверка оплаты из бота (ответ `paid` / `unpaid`), например для 2900:

```
https://script.google.com/macros/s/AKfycby5bCIbi2CQ4u0z3oeZu0v41c6DdKlhLL_v5SAQE3gINap9evvCjxEX-icPbGEl61jZ/exec?api=check&email={{email_camp_norecs}}&offer=1a49501b-ff80-40da-8841-de64f310d9b4&format=text
```

Без `&offer=…` — проверка оплаты любого тарифа.

## Важно

- API-ключ Lava, токен PuzzleBot и названия команд хранятся **только** в Apps Script.
- После правок `deepbodycamp.gs` вставьте код в Apps Script и выпустите новую версию развёртывания
  (Развертывание → Управление развертываниями → карандаш → Новая версия), чтобы адрес `/exec` не поменялся.
