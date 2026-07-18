# Получение списка магазинов-партнеров

## Общая информация

| Параметр | Значение |
|----------|----------|
| Метод | GET |
| URL | `/api/v1/shops/partners` |
| Описание | Возвращает список магазинов-партнеров с информацией о ближайшей доставке |
| Авторизация | Bearer Token |

---

## Query Parameters

| Параметр | Тип | Обязательный | Описание |
|----------|-----|--------------|----------|
| cityId | integer | Да | Идентификатор города. |
| page | integer | Нет | Номер страницы. Минимальное значение — **1**. По умолчанию — **1**. |
| size | integer | Нет | Количество элементов на странице. Допустимые значения: **1–100**. По умолчанию — **20**. |

---

## Request Headers

| Заголовок | Значение | Обязательный |
|------------|----------|--------------|
| Authorization | Bearer Token | Да |

---

## Пример запроса

```http
GET /api/v1/shops/partners?cityId=1&page=1&size=20 HTTP/1.1
Authorization: Bearer eyJhb...
```

---

## Response Body

| Поле | Тип | Описание |
|------|-----|----------|
| page | integer | Номер текущей страницы. |
| size | integer | Количество элементов на странице. |
| totalElements | integer | Общее количество найденных магазинов. |
| totalPages | integer | Общее количество страниц. |
| items | array | Список магазинов-партнеров. |

### Объект `items`

| Поле | Тип | Описание |
|------|-----|----------|
| id | integer | Уникальный идентификатор магазина. |
| name | string | Название магазина. |
| deliveryType | string | Тип доставки. Возможные значения: `SCHEDULED` — доставка по временному интервалу, `FAST` — экспресс-доставка. |
| deliveryFrom | datetime | Начало ближайшего интервала доставки. Заполняется только для `SCHEDULED`. Формат ISO 8601. |
| deliveryTo | datetime | Конец ближайшего интервала доставки. Заполняется только для `SCHEDULED`. Формат ISO 8601. |
| deliveryMin | integer | Минимальное время доставки в минутах. Заполняется только для `FAST`. |
| deliveryMax | integer | Максимальное время доставки в минутах. Заполняется только для `FAST`. |
| logoUrl | string (URL) | URL логотипа магазина. |
| shopUrl | string (URL) | URL страницы магазина. |

---

## Успешный ответ

### 200 OK

```json
{
  "page": 1,
  "size": 20,
  "totalElements": 3,
  "totalPages": 1,
  "items": [
    {
      "id": 1,
      "name": "METRO",
      "deliveryType": "SCHEDULED",
      "deliveryFrom": "2026-07-18T21:00:00+03:00",
      "deliveryTo": "2026-07-18T23:00:00+03:00",
      "logoUrl": "https://cdn.example.com/logos/metro.png",
      "shopUrl": "https://metro.example.com"
    },
    {
      "id": 2,
      "name": "Ашан",
      "deliveryType": "SCHEDULED",
      "deliveryFrom": "2026-07-18T18:00:00+03:00",
      "deliveryTo": "2026-07-18T20:00:00+03:00",
      "logoUrl": "https://cdn.example.com/logos/auchan.png",
      "shopUrl": "https://auchan.example.com"
    },
    {
      "id": 3,
      "name": "ВкусВилл",
      "deliveryType": "FAST",
      "deliveryMin": 20,
      "deliveryMax": 60,
      "logoUrl": "https://cdn.example.com/logos/vkusvill.png",
      "shopUrl": "https://vkusvill.example.com"
    }
  ]
}
```

---

## Возможные ошибки

### 400 BAD_REQUEST

Некорректные параметры запроса.

Например:

- отсутствует обязательный параметр `cityId`;
- `page < 1`;
- `size` выходит за допустимый диапазон.

```json
{
  "code": "BAD_REQUEST",
  "message": "Parameter 'cityId' is required."
}
```

---

### 401 UNAUTHORIZED

Пользователь не авторизован.

```json
{
  "code": "UNAUTHORIZED",
  "message": "Authentication required."
}
```

---

### 500 INTERNAL_ERROR

Внутренняя ошибка сервера.

```json
{
  "code": "INTERNAL_ERROR",
  "message": "Unexpected server error."
}
```

---

## Бизнес-правила

1. Метод возвращает только магазины-партнеры для указанного города.
2. Магазины сортируются по возрастанию времени ближайшей доставки.
3. Для магазинов с `deliveryType = FAST` возвращаются поля `deliveryMin` и `deliveryMax`.
4. Для магазинов с `deliveryType = SCHEDULED` возвращаются поля `deliveryFrom` и `deliveryTo`.
5. Поля `logoUrl` и `shopUrl` возвращаются для каждого магазина.
6. Если магазины для указанного города отсутствуют, возвращается пустой список `items`.
7. При отсутствии результатов возвращается статус **200 OK**.
