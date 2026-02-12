# Полная документация API

# Содержание
- [Аутентификация](#аутентификация)
- [REST API](#rest-api-v1-json)
- [События (Events)](#события-events)
- [Персоны (Persons)](#персоны-persons)
- [Справочники](#справочники)
- [Связи (Many-to-Many)](#связи-many-to-many)


Справочники:

1. street
2. profession
3. book
4. keyword

Основные:

5. event
6. person

Связи M:N:

7. person-event
8. person-keyword
9. event-keyword
10. person-book
11. event-book
12. person-profession
13. person-street

## Аутентификация

При необходимости создание нового пользователя

```
python manage.py createsuperuser
```

### Получение токена

**Endpoint:**
```
POST http://127.0.0.1:8000/api/v1/auth/login/
```

**Описание:** Получить токен для аутентификации

**Тело запроса:**
```json
{
    "username": "username",
    "password": "password"
}
```

**Ответ (200 OK):**
```json
{
    "token": "ВАШ_ТОКЕН_ЗДЕСЬ"
}
```

**Пример с curl:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/auth/login/ \
  -H "Content-Type: application/json" \
  -d '{"username": "username", "password": "password"}'
```

**Пример с Postman:**
1. Метод: POST
2. URL: `http://127.0.0.1:8000/api/v1/auth/login/`
3. Headers: `Content-Type: application/json`
4. Body (raw JSON):
   ```json
   {
       "username": "username",
       "password": "password"
   }
   ```


---

### Использование токена

**Все запросы, требующие аутентификации (POST/PUT/PATCH/DELETE), должны включать заголовок:**

```
Authorization: Token ВАШ_ТОКЕН_ЗДЕСЬ
```

**Пример с curl:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/event/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Token ВАШ_ТОКЕН_ЗДЕСЬ" \
  -d '{"title": "Новое событие", "date": "2024-01-01"}'
```

**Пример с Postman:**
1. Headers tab
2. Key: `Authorization`
3. Value: `Token ВАШ_ТОКЕН_ЗДЕСЬ`

---

### Методы аутентификации

Система поддерживает **два метода аутентификации**:

#### 1. Token Authentication (для Next.js, мобильных приложений)
```
Authorization: Token ВАШ_ТОКЕН_ЗДЕСЬ
```

#### 2. Session Authentication (для Browsable API в браузере)
- Войдите в `/admin/`
- Откройте Browsable API в том же браузере
- Аутентификация произойдет автоматически через session cookie

---

### Права доступа

| HTTP Метод | Требует аутентификации | Описание |
| :--- | :--- | :--- |
| GET | ❌ Нет | Чтение данных |
| OPTIONS | ❌ Нет | Метаданные |
| HEAD | ❌ Нет | Заголовки |
| POST | ✅ Да | Создание |
| PUT | ✅ Да | Полное обновление |
| PATCH | ✅ Да | Частичное обновление |
| DELETE | ✅ Да | Удаление |

---

### Ошибки аутентификации

**Попытка изменить данные без токена:**

**Запрос:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/event/ \
  -H "Content-Type: application/json" \
  -d '{"title": "Тест", "date": "2024-01-01"}'
```

**Ответ (401 Unauthorized):**
```json
{
    "detail": "Учетные данные не были предоставлены."
}
```

---

**Неверный токен:**

**Запрос:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/event/ \
  -H "Authorization: Token INVALID_TOKEN" \
  -d '{"title": "Тест"}'
```

**Ответ (401 Unauthorized):**
```json
{
    "detail": "Недопустимый токен."
}
```

---

**Неверные учетные данные при логине:**

**Запрос:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/auth/login/ \
  -H "Content-Type: application/json" \
  -d '{"username": "username", "password": "wrong_password"}'
```

**Ответ (400 Bad Request):**
```json
{
    "non_field_errors": [
        "Невозможно войти с предоставленными учетными данными."
    ]
}
```

---

## REST API v1 (JSON)

### Базовая информация

**Базовый URL:**
```
Development:  http://127.0.0.1:8000/api/v1/
Production:   https://dates1.pr-cbs.ru/api/v1/
```

**Формат ответа:** JSON

**Пагинация:** 12 элементов на страницу

**Аутентификация:**
- GET запросы — **публичные** (без токена)
- POST/PUT/PATCH/DELETE — **требуется токен**

---

### API Root

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/
```

**Аутентификация:** ❌ Не требуется

**Описание:** Список всех доступных endpoints

**Ответ:**
```json

{
    "street": "http://127.0.0.1:8000/api/v1/street/",
    "profession": "http://127.0.0.1:8000/api/v1/profession/",
    "book": "http://127.0.0.1:8000/api/v1/book/",
    "keyword": "http://127.0.0.1:8000/api/v1/keyword/",
    "event": "http://127.0.0.1:8000/api/v1/event/",
    "person": "http://127.0.0.1:8000/api/v1/person/",
    "event-book": "http://127.0.0.1:8000/api/v1/event-book/",
    "person-event": "http://127.0.0.1:8000/api/v1/person-event/",
    "person-keyword": "http://127.0.0.1:8000/api/v1/person-keyword/",
    "event-keyword": "http://127.0.0.1:8000/api/v1/event-keyword/",
    "person-book": "http://127.0.0.1:8000/api/v1/person-book/",
    "person-profession": "http://127.0.0.1:8000/api/v1/person-profession/",
    "person-street": "http://127.0.0.1:8000/api/v1/person-street/"
}
```

---

## События (Events)

(EventListSerializer)

### 1. Список событий

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/event/
```

**Query параметры:**
- `?page=N` — номер страницы
- `?date=YYYY-MM-DD` — фильтр по дате
- `?street=ID` — фильтр по улице
- `?search=TEXT` — поиск в названии и описании
- `?ordering=FIELD` — сортировка (date, title, -date, -title)

**Примеры:**
```
GET /api/v1/event/                          → первая страница
GET /api/v1/event/?page=2                   → вторая страница
GET /api/v1/event/?date=1983-01-01          → события 1 января 1983
GET /api/v1/event/?street=175               → события на Каменноостровском пр.
GET /api/v1/event/?search=памятник          → поиск "памятник"
GET /api/v1/event/?ordering=date            → по дате (старые→новые)
GET /api/v1/event/?ordering=-title          → по названию (Я→А)
```

**Комбинированные запросы:**
```
GET /api/v1/event/?date=1983-01-01&ordering=-title
GET /api/v1/event/?search=памятник&ordering=date
GET /api/v1/event/?street=175&ordering=-date&page=2
```


**Структура полей:**

| Поле | Тип | Описание | Пример |
|------|-----|----------|--------|
| `id` | integer | ID события | 586 |
| `title` | string | Название | "Открыта мемориальная доска..." |
| `date` | string | Дата (ISO) | "2020-10-29" |
| **`day`** | **integer** | **День месяца** | **29** |
| **`month`** | **integer** | **Номер месяца** | **10** |
| `image` | string/null | URL изображения | "http://..." |
| **`street`** | **object/null** | **Объект улицы** | **{id, name}** |

```
{
    "count": 198,
    "next": "http://127.0.0.1:8000/api/v1/event/?page=2",
    "previous": null,
    "results": [
        {
            "id": 591,
            "title": "1",
            "date": "2026-02-12",
            "day": 12,
            "month": 2,
            "image": null,
            "street": {
                "id": 175,
                "name": "Каменноостровский проспект"
            }
        },
...
```

---

### 2. Детали события

(EventDetailSerializer)

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/event/{id}/
```

**Пример:**
```
GET /api/v1/event/394/
```

```
{
    "id": 394,
    "title": "Открыта мемориальная доска государственному деятелю  П.С. Попкову",
    "date": "1983-01-01",
    "day": 1,
    "month": 1,
    "description_html": "<p>В 1983 году на доме № 29/37 по Кронверкской улице была установлена мемориальная доска <a href=\"https://dates.pr-cbs.ru/persons/popkov/\">Попкову Пётру Сергеевичу</a>, государственному и партийному деятелю, одному из организаторов и руководителей обороны Ленинграда в годы Великой Отечественной войны.</p>\n<p>Надпись на гранитной мемориальной доске сообщает: «В этом доме с 1939 по 1950 год жил Петр Сергеевич Попков, председатель Исполкома Ленинградского городского Совета депутатов трудящихся в годы Великой Отечественной войны». Автор мемориальной доски -  скульптор Исаева Вера Васильевна.</p>\n<p>Пётр Сергеевич Попков (1903-1950) - советский партийный деятель. С апреля 1942 года — член Военного совета Ленинградской армии ПВО. 13 января 1944 года вместе с Алексеем Бубновым подписал решение Ленгорисполкома о возвращении 20 площадям и улицам Ленинграда их исторических дореволюционных названий (в том числе Дворцовой площади, Невскому проспекту и Садовой улице).</p>\n<p>В 1949 году был арестован; являлся одним из главных фигурантов «Ленинградского дела» — послевоенных чисток в партаппарате. 1 октября 1950 года был расстрелян. В 1954 году военной коллегией Верховного Суда СССР приговор как фальсифицированный был отменён, и П. С. Попков полностью реабилитирован (посмертно).</p>\n<p><a href=\"https://pr-cbs.ru/catalog/-/books/11132313-predannyy-zabveniyu\">Амосова, А. А. Преданный забвению : политическая биография Петра Попкова, 1937-1950 / А. А. Амосова. - Санкт-Петербург : Алетейя, 2016.</a></p>",
    "image": "http://127.0.0.1:8000/media/full/2bf6d0257a45fa73792669f135ee961ac2a1fb60.jpg",
    "street": null,
    "persons": [],
    "keywords": [],
    "books": []
}
```

---

### 3. Создание события

**Endpoint:**
```
POST http://127.0.0.1:8000/api/v1/event/
```

**Headers:**
```
Content-Type: application/json
Authorization: Token ВАШ_ТОКЕН_ЗДЕСЬ
```

**Тело запроса:**
```json
{
    "title": "Новое событие",
    "date": "2024-01-01",
    "description_html": "<p>Описание события</p>",
    "image": null,
    "street_id": 175
}
```

**Пример с curl:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/event/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026" \
  -d '{
    "title": "Новое событие",
    "date": "2024-01-01",
    "description_html": "<p>Описание события</p>",
    "image": null
    "street": 175
  }'
```


**Ответ (201 Created):**
```json
{
    "id": 589,
    "title": "Новое событие",
    "date": "2024-01-01",
    "day": 1,                           // ← Автоматически вычислено
    "month": 1,                         // ← Автоматически вычислено
    "description_html": "<p>Описание события</p>",
    "image": null,
    "street": {                         // ← Автоматически преобразовано в объект
        "id": 175,
        "name": "Каменноостровский проспект"
    },
    "persons": [],
    "keywords": [],
    "books": []
}
```

---

### 4. Полное обновление события

**Endpoint:**
```
PUT http://127.0.0.1:8000/api/v1/event/{id}/
```

**Аутентификация:** ✅ **Требуется токен**

**Headers:**
```
Content-Type: application/json
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

**Тело запроса:**
```json
{
    "title": "Новое событие 1",
    "date": "2024-01-01",
    "description_html": "<p>Описание события</p>",
    "image": null,
    "street_id": 175
}
```

**Ответ (200 OK):**
```
{
    "id": 593,
    "title": "Новое событие 1",
    "date": "2024-01-01",
    "day": 1,
    "month": 1,
    "description_html": "<p>Описание события</p>",
    "image": null,
    "street": {
        "id": 175,
        "name": "Каменноостровский проспект"
    },
    "persons": [],
    "keywords": [],
    "books": []
}
```

---

### 5. Частичное обновление события

**Endpoint:**
```
PATCH http://127.0.0.1:8000/api/v1/event/{id}/
```

**Аутентификация:** ✅ **Требуется токен**

**Headers:**
```
Content-Type: application/json
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

**Тело запроса (только изменяемые поля):**

```json
{
    "title": "Только новое название"
}
```

**Ответ (200 OK):**
```
{
    "id": 593,
    "title": "Только новое название",
    "date": "2024-01-01",
    "day": 1,
    "month": 1,
    "description_html": "<p>Описание события</p>",
    "image": null,
    "street": {
        "id": 175,
        "name": "Каменноостровский проспект"
    },
    "persons": [],
    "keywords": [],
    "books": []
}
```

---

### 6. Удаление события

**Endpoint:**
```
DELETE http://127.0.0.1:8000/api/v1/event/{id}/
```

**Аутентификация:** ✅ **Требуется токен**

**Headers:**
```
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

**Пример с curl:**
```bash
curl -X DELETE http://127.0.0.1:8000/api/v1/event/502/ \
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026"
```


**Ответ:** 204 No Content (пустое тело)

---

## Персоны (Persons)

### 1. Список персон

(PersonListSerializer)

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/person/
```

**Query параметры:**
- `?page=N` — номер страницы
- `?search=TEXT` — поиск по ФИО
- `?full_text=TEXT` — полнотекстовый поиск (ФИО + биография)
- `?ordering=FIELD` — сортировка (last_name, birth_date, -last_name, -birth_date)

**Примеры:**
```
GET /api/v1/person/                         → первая страница
GET /api/v1/person/?page=2                  → вторая страница
GET /api/v1/person/?search=Абрамов          → поиск по ФИО
GET /api/v1/person/?full_text=писатель      → поиск в биографии
GET /api/v1/person/?ordering=last_name      → по фамилии (А→Я)
GET /api/v1/person/?ordering=-last_name     → по фамилии (Я→А)
GET /api/v1/person/?ordering=birth_date     → по дате рождения (старые→молодые)
GET /api/v1/person/?ordering=-birth_date    → по дате рождения (молодые→старые)
```

**Отличие search от full_text:**
```
?search=Абрамов       → ищет только в ФИО
?full_text=Абрамов    → ищет в ФИО + description_html + article_html
```

**Ответ: 200 OK**
```json
{
    "count": 208,
    "next": "http://127.0.0.1:8000/api/v1/person/?page=2",
    "previous": null,
    "results": [
        {
            "id": 210,
            "last_name": "Абрамов",
            "first_name": "Фёдор",
            "middle_name": "Александрович",
            "full_name": "Абрамов Фёдор Александрович",   
            "short_name": "Абрамов Ф.А.",                 
            "birth_date": "1920-02-29",
            "death_date": "1983-05-14",
            "image": "http://127.0.0.1:8000/media/full/516e2ba0174ab51a5e13cc2bb5953d5fab2b1889.jpg"
        },
        {
            "id": 211,
            "last_name": "Агнивцев",
            "first_name": "Николай",
            "middle_name": "Яковлевич",
            "full_name": "Агнивцев Николай Яковлевич",   
            "short_name": "Агнивцев Н.Я.",                  
            "birth_date": "1888-04-20",
            "death_date": "1932-10-29",
            "image": "http://127.0.0.1:8000/media/full/a10104b1688bb2b135b9dc2eba786b9fe8d471ee.jpg"
        }
    ]
}
```

---

### 2. Детали персоны

(PersonDetailSerializer)

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/person/{id}/
```

**Аутентификация:** ❌ Не требуется

**Примеры:**
```
GET /api/v1/person/210/  (Абрамов Ф.А.)
GET /api/v1/person/211/  (Агнивцев Н.Я.)
GET /api/v1/person/214/  (Алфёров Ж.И.)
```

Ответ: 200 OK

```
{
    "id": 210,
    "last_name": "Абрамов",
    "first_name": "Фёдор",
    "middle_name": "Александрович",
    "full_name": "Абрамов Фёдор Александрович",    
    "short_name": "Абрамов Ф.А.",            
    "birth_date": "1920-02-29",
    "death_date": "1983-05-14",
    "description_html": "<p>Краткое описание...</p>",
    "article_html": "<p>Полная биография...</p>",
    "image": "http://127.0.0.1:8000/media/full/516e2ba0174ab51a5e13cc2bb5953d5fab2b1889.jpg",
    
    "professions": [                            
        {
            "id": 2,
            "name": "Писатель"
        },
        {
            "id": 1,
            "name": "Литературовед"
        }
    ],
    
    "streets": [                                
        {
            "id": 175,
            "name": "Каменноостровский проспект"
        }
    ],
    
    "books": [                                      
        {
            "id": 8,
            "author": "Иванов И.И.",
            "title": "История Петроградского района",
            "url": "http://example.com",
            "image": "http://127.0.0.1:8000/media/books_images/book_8.jpg"
        }
    ],
    
    "keywords": [                                  
        {
            "id": 7,
            "keyword": "литература"
        }
    ],
    
    "events": [                                     
        {
            "id": 394,
            "title": "Открыта мемориальная доска П.С. Попкову",
            "date": "1983-01-01",
            "day": 1,
            "month": 1,
            "image": "http://127.0.0.1:8000/media/full/2bf6d0257a45fa73792669f135ee961ac2a1fb60.jpg",
            "street": {
                "id": 175,
                "name": "Каменноостровский проспект"
            }
        }
    ]
}
```

---

### 3. Создание персоны

**Endpoint:**
```
POST http://127.0.0.1:8000/api/v1/person/
```

**Аутентификация:** ✅ **Требуется токен**

**Headers:**
```
Content-Type: application/json
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

**Тело запроса:**
```json
{
    "last_name": "Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "birth_date": "1950-01-01",
    "death_date": null,
    "description_html": "<p>Краткое описание</p>",
    "article_html": "<p>Полная биография</p>"
}
```


**Ответ: 201 Created**
```
{
    "id": 420,
    "last_name": "Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "full_name": "Иванов Иван Иванович",
    "short_name": "Иванов И.И.",
    "birth_date": "1950-01-01",
    "death_date": null,
    "description_html": "<p>Краткое описание</p>",
    "article_html": "<p>Полная биография</p>",
    "image": null,
    "professions": [],
    "streets": [],
    "books": [],
    "keywords": [],
    "events": []
}
```

---

### 4. Обновление персоны

**PUT** — полное обновление (все поля):
```
PUT http://127.0.0.1:8000/api/v1/person/{id}/
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

**PATCH** — частичное обновление:
```
PATCH http://127.0.0.1:8000/api/v1/person/{id}/
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

---

### 5. Удаление персоны

**Endpoint:**
```
DELETE http://127.0.0.1:8000/api/v1/person/{id}/
```

---

## Справочники

Все справочники (улицы, ключевые слова, профессии, книги) работают аналогично:

### Чтение (GET) — без токена ❌
```
GET /api/v1/street/
GET /api/v1/keyword/
GET /api/v1/profession/
GET /api/v1/book/
```

### Создание/Изменение/Удаление — с токеном ✅
```
POST   /api/v1/street/     + Token
PUT    /api/v1/street/{id}/ + Token
PATCH  /api/v1/street/{id}/ + Token
DELETE /api/v1/street/{id}/ + Token
```
---


## Справочники

### 1. Улицы (Streets)

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/street/
```

**Ответ: 200 OK**
```json
[
    {
        "id": 175,
        "name": "Каменноостровский проспект"
    },
    {
        "id": 176,
        "name": "Мичуринская улица"
    },
    {
        "id": 177,
        "name": "Кронверкский проспект"
    }
]
```

---

### 2. Профессии (Professions)

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/profession/
```

**Ответ: 200 OK**
```json
[
    {
        "id": 1,
        "name": "Литературовед"
    },
    {
        "id": 2,
        "name": "Писатель"
    },
    {
        "id": 6,
        "name": "Публицист"
    }
]
```

---

### 3. Ключевые слова (Keywords)

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/keyword/
```

**Ответ: 200 OK**
```json
[
    {
        "id": 8,
        "keyword": "Морской дом"
    },
    {
        "id": 7,
        "keyword": "Народное ополчение"
    }
]
```

---

### 4. Книги (Books)

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/book/
```

**Ответ: 200 OK**
```json
[
    {
        "id": 8,
        "author": "Абрамов Ф. А.",
        "title": "Братья и сестры. Роман в четырёх книгах",
        "url": "https://pr-cbs.ru/catalog/-/books/10397321-brat-ya-i-sestry",
        "image": "books_images/boocover.jpeg"
    }
]
```


---

## Связи (Many-to-Many)

### Создание связей

Все промежуточные таблицы работают одинаково.

**Общий паттерн:**
```
POST /api/v1/{resource}-{resource}/
Authorization: Token YOUR_TOKEN

{
    "{resource1}_id": 123,
    "{resource2}_id": 456
}
```

---

### 1. Person ↔ Event

**POST /api/v1/person-event/**

```json
{
    "person": 210,
    "event": 394
}
```

---

### 2. Person ↔ Keyword

**POST /api/v1/person-keyword/**

```json
{
    "person": 210,
    "keyword": 7
}
```

---

### 3. Event ↔ Keyword

**POST /api/v1/event-keyword/**

```json
{
    "event": 394,
    "keyword": 7
}
```

---

### 4. Person ↔ Book

**POST /api/v1/person-book/**

```json
{
    "person": 210,
    "book": 8
}
```

---

### 5. Event ↔ Book

**POST /api/v1/event-book/**

```json
{
    "event": 394,
    "book": 8
}
```

---

### 6. Person ↔ Profession

**POST /api/v1/person-profession/**

```json
{
    "person": 210,
    "profession": 1
}
```

### 7. Person ↔ street

**POST /api/v1/person-street/**

```json
{
    "person": 210,
    "street": 1
}
```



---

## Сводная таблица атрибутов

| Связь | Endpoint | Атрибуты |
|-------|----------|----------|
| Person ↔ Event | `/api/v1/person-event/` | `person_id`, `event_id` |
| Person ↔ Keyword | `/api/v1/person-keyword/` | `person_id`, `keyword_id` |
| Event ↔ Keyword | `/api/v1/event-keyword/` | `event_id`, `keyword_id` |
| Person ↔ Book | `/api/v1/person-book/` | `person_id`, `book_id` |
| Event ↔ Book | `/api/v1/event-book/` | `event_id`, `book_id` |
| Person ↔ Profession | `/api/v1/person-profession/` | `person_id`, `profession_id` |
| Person ↔ street | `/api/v1/person-street/` | `person_id`, `street_id` |

---

