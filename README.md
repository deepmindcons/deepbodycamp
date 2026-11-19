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
   2500 → `COMMAND_NO_REC`, 3500, 4500 и 5500 → `COMMAND_REC`.
6. На листах «Без записей» (2500) и «С записями» (3500, 4500, 5500) в столбце E «Оплата» ставит:
   «Успешно» — оплачен тариф этого листа, «Оплачен другой тариф: …», «Нет оплаты».
7. Лист «Итоги» считается формулами таблицы, скрипт его не трогает.
   Раз в 5 минут скрипт сам перепроверяет неоплаченные счета.

## Настройка

1. Таблица → Расширения → Apps Script → вставить `deepbodycamp.gs`.
2. Вверху скрипта вписать `LAVA_API_KEY`, `PUZZLEBOT_TOKEN` и названия команд бота
   `COMMAND_NO_REC` (2300) и `COMMAND_REC` (3300, 4300).
   Ключ, токен и названия команд вписываются только в Apps Script — в GitHub их не храним.
   ID цен четырёх тарифов вписаны в `TARIFFS`.
3. Развернуть как веб-приложение (запуск от моего имени, доступ — все), адрес `/exec`
   вписать в `SCRIPT_URL` в `docs/index.html`.
4. Меню «Lava» → «1. Включить автопроверку».

## Ссылки для кнопок в боте

| Тариф | Ссылка |
|---|---|
| 2500 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{EMAIL_БЕЗ_ЗАПИСЕЙ}}&offer=ID_ЦЕНЫ_2500` |
| 3500 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{EMAIL_С_ЗАПИСЯМИ}}&offer=ID_ЦЕНЫ_3500` |
| 4500 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{EMAIL_С_ЗАПИСЯМИ}}&offer=ID_ЦЕНЫ_4500` |
| 5500 ₽ | `https://deepmindcons.github.io/deepbodycamp/?uid={{USERNAME_TEXT}}&tid={{USER_ID_TEXT}}&email={{EMAIL_С_ЗАПИСЯМИ}}&offer=ID_ЦЕНЫ_5500` |

Проверка оплаты из бота (ответ `paid` / `unpaid`), например для 2500:

```
https://script.google.com/macros/s/AKfycby5bCIbi2CQ4u0z3oeZu0v41c6DdKlhLL_v5SAQE3gINap9evvCjxEX-icPbGEl61jZ/exec?api=check&email={{EMAIL_БЕЗ_ЗАПИСЕЙ}}&offer=ID_ЦЕНЫ_2500&format=text
```

Без `&offer=…` — проверка оплаты любого тарифа.

## Важно

- API-ключ Lava, токен PuzzleBot и названия команд хранятся **только** в Apps Script.
- После правок `deepbodycamp.gs` вставьте код в Apps Script и выпустите новую версию развёртывания
  (Развертывание → Управление развертываниями → карандаш → Новая версия), чтобы адрес `/exec` не поменялся.
