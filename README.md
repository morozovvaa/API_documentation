# Полная документация API

## Аутентификация

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
    "token": "de4be75834b182327dfaa9bc111bdda6381e1026"
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
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026" \
  -d '{"title": "Новое событие", "date": "2024-01-01"}'
```

**Пример с Postman:**
1. Headers tab
2. Key: `Authorization`
3. Value: `Token de4be75834b182327dfaa9bc111bdda6381e1026`

---

### Методы аутентификации

Система поддерживает **два метода аутентификации**:

#### 1. Token Authentication (для Next.js, мобильных приложений)
```
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

#### 2. Session Authentication (для Browsable API в браузере)
- Войдите в `/admin/`
- Откройте Browsable API в том же браузере
- Аутентификация произойдет автоматически через session cookie

---

### Права доступа

```
┌─────────────────┬──────────────────────┬─────────────────────┐
│ HTTP Метод      │ Требует аутентификации│ Описание           │
├─────────────────┼──────────────────────┼─────────────────────┤
│ GET             │ ❌ Нет               │ Чтение данных      │
│ OPTIONS         │ ❌ Нет               │ Метаданные         │
│ HEAD            │ ❌ Нет               │ Заголовки          │
│ POST            │ ✅ Да                │ Создание           │
│ PUT             │ ✅ Да                │ Полное обновление  │
│ PATCH           │ ✅ Да                │ Частичное обновление│
│ DELETE          │ ✅ Да                │ Удаление           │
└─────────────────┴──────────────────────┴─────────────────────┘
```

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
  -d '{"username": "admin", "password": "wrong_password"}'
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
    "event": "http://127.0.0.1:8000/api/v1/event/",
    "event-book": "http://127.0.0.1:8000/api/v1/event-book/",
    "profession": "http://127.0.0.1:8000/api/v1/profession/",
    "book": "http://127.0.0.1:8000/api/v1/book/",
    "person": "http://127.0.0.1:8000/api/v1/person/",
    "person-event": "http://127.0.0.1:8000/api/v1/person-event/",
    "keyword": "http://127.0.0.1:8000/api/v1/keyword/",
    "person-keyword": "http://127.0.0.1:8000/api/v1/person-keyword/",
    "event-keyword": "http://127.0.0.1:8000/api/v1/event-keyword/",
    "person-book": "http://127.0.0.1:8000/api/v1/person-book/",
    "person-profession": "http://127.0.0.1:8000/api/v1/person-profession/"
}
```

---

## События (Events)

### 1. Список событий

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/event/
```

**Аутентификация:** ❌ Не требуется

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
GET /api/v1/event/?ordering=-date           → по дате (новые→старые)
GET /api/v1/event/?ordering=title           → по названию (А→Я)
GET /api/v1/event/?ordering=-title          → по названию (Я→А)
```

**Комбинированные запросы:**
```
GET /api/v1/event/?date=1983-01-01&ordering=-title
GET /api/v1/event/?search=памятник&ordering=date
GET /api/v1/event/?street=175&ordering=-date&page=2
```

**Ответ:**
```json
{
    "count": 195,
    "next": "http://127.0.0.1:8000/api/v1/event/?page=2",
    "previous": null,
    "results": [
        {
            "id": 400,
            "title": "Заложена Петропавловская крепость",
            "date": "1703-05-27",
            "description_html": "<p>27 мая 1703 года...</p>",
            "image": "events_images/abc123.jpg",
            "street": null
        }
    ]
}
```

---

### 2. Детали события

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/event/{id}/
```

**Аутентификация:** ❌ Не требуется

**Примеры:**
```
GET /api/v1/event/394/
GET /api/v1/event/395/
GET /api/v1/event/401/
```

**Ответ:**
```json
{
    "id": 394,
    "title": "Открыта мемориальная доска П.С. Попкову",
    "date": "1983-01-01",
    "description_html": "<p>В 1983 году на доме № 29/37...</p>",
    "image": "full/2bf6d0257a45fa73792669f135ee961ac2a1fb60.jpg",
    "street": null
}
```

---

### 3. Создание события

**Endpoint:**
```
POST http://127.0.0.1:8000/api/v1/event/
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
    "title": "Новое событие",
    "date": "2024-01-01",
    "description_html": "<p>Описание события</p>",
    "street": 175
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
    "street": 175
  }'
```

**Пример с Next.js:**
```typescript
const token = localStorage.getItem('authToken')

const response = await fetch('http://127.0.0.1:8000/api/v1/event/', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Token ${token}`,
  },
  body: JSON.stringify({
    title: 'Новое событие',
    date: '2024-01-01',
    description_html: '<p>Описание события</p>',
    street: 175,
  }),
})

const data = await response.json()
```

**Ответ (201 Created):**
```json
{
    "id": 589,
    "image": null,
    "title": "Новое событие",
    "date": "2024-01-01",
    "description_html": "<p>Описание события</p>",
    "street": 175
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

**Тело запроса (все поля обязательны):**
```json
{
    "title": "Обновленное название",
    "date": "1983-01-01",
    "description_html": "<p>Новое описание</p>",
    "street": 175
}
```

**Пример с curl:**
```bash
curl -X PUT http://127.0.0.1:8000/api/v1/event/502/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026" \
  -d '{
    "title": "Обновленное название",
    "date": "1983-01-01",
    "description_html": "<p>Новое описание</p>",
    "street": 175
  }'
```
**Ответ (200 OK):**
```
{
    "id": 589,
    "image": null,
    "title": "Обновленное название",
    "date": "1983-01-01",
    "description_html": "<p>Новое описание</p>",
    "street": 175
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

**Пример с curl:**
```bash
curl -X PATCH http://127.0.0.1:8000/api/v1/event/502/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026" \
  -d '{"title": "Только новое название"}'
```

**Ответ (200 OK):**
```
{
    "id": 589,
    "image": null,
    "title": "Только новое название",
    "date": "1983-01-01",
    "description_html": "<p>Новое описание</p>",
    "street": 175
}
```

**Пример с Next.js:**
```typescript
const token = localStorage.getItem('authToken')

const response = await fetch(`http://127.0.0.1:8000/api/v1/event/${id}/`, {
  method: 'PATCH',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': `Token ${token}`,
  },
  body: JSON.stringify({
    title: 'Только новое название',
  }),
})
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

**Пример с Next.js:**
```typescript
const token = localStorage.getItem('authToken')

const response = await fetch(`http://127.0.0.1:8000/api/v1/event/${id}/`, {
  method: 'DELETE',
  headers: {
    'Authorization': `Token ${token}`,
  },
})

if (response.ok) {
  console.log('Событие удалено')
}
```

**Ответ:** 204 No Content (пустое тело)

---

## Персоны (Persons)

### 1. Список персон

**Endpoint:**
```
GET http://127.0.0.1:8000/api/v1/person/
```

**Аутентификация:** ❌ Не требуется

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

**Ответ:**
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
            "birth_date": "1920-02-29",
            "death_date": "1983-05-14",
            "description_html": "Писатель, литературовед...",
            "article_html": "<p>Федор Абрамов родился...</p>",
            "image": "persons_images/516e2ba0174ab51a5e13cc2bb5953d5fab2b1889.jpg"
        }
    ]
}
```

---

### 2. Детали персоны

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

**Пример с curl:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/person/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026" \
  -d '{
    "last_name": "Иванов",
    "first_name": "Иван",
    "middle_name": "Иванович",
    "birth_date": "1950-01-01",
    "description_html": "<p>Краткое описание</p>"
  }'
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

**Аутентификация:** ✅ **Требуется токен**

**Headers:**
```
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

---

## 🏷️ Справочники

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

## 🔗 Связи (Many-to-Many)

Все промежуточные таблицы работают одинаково:

### Чтение связей — без токена ❌
```
GET /api/v1/person-event/
GET /api/v1/person-keyword/
GET /api/v1/event-keyword/
GET /api/v1/person-book/
GET /api/v1/event-book/
GET /api/v1/person-profession/
```

### Создание связи — с токеном ✅

**Пример: Связать персону с ключевым словом**

**Endpoint:**
```
POST http://127.0.0.1:8000/api/v1/person-keyword/
```

**Headers:**
```
Content-Type: application/json
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

**Тело запроса:**
```json
{
    "person_id": 210,
    "keyword_id": 7
}
```

**Пример с curl:**
```bash
curl -X POST http://127.0.0.1:8000/api/v1/person-keyword/ \
  -H "Content-Type: application/json" \
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026" \
  -d '{"person_id": 210, "keyword_id": 7}'
```

---

### Удаление связи — с токеном ✅

**Endpoint:**
```
DELETE http://127.0.0.1:8000/api/v1/person-keyword/{id}/
```

**Headers:**
```
Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026
```

**Пример с curl:**
```bash
curl -X DELETE http://127.0.0.1:8000/api/v1/person-keyword/8/ \
  -H "Authorization: Token de4be75834b182327dfaa9bc111bdda6381e1026"
```

---

## 📊 Сводная таблица методов и аутентификации

| Endpoint | GET | POST | PUT | PATCH | DELETE |
|----------|-----|------|-----|-------|--------|
| `/api/v1/event/` | ❌ Публичный | ✅ Токен | ✅ Токен | ✅ Токен | ✅ Токен |
| `/api/v1/person/` | ❌ Публичный | ✅ Токен | ✅ Токен | ✅ Токен | ✅ Токен |
| `/api/v1/street/` | ❌ Публичный | ✅ Токен | ✅ Токен | ✅ Токен | ✅ Токен |
| `/api/v1/keyword/` | ❌ Публичный | ✅ Токен | ✅ Токен | ✅ Токен | ✅ Токен |
| `/api/v1/profession/` | ❌ Публичный | ✅ Токен | ✅ Токен | ✅ Токен | ✅ Токен |
| `/api/v1/book/` | ❌ Публичный | ✅ Токен | ✅ Токен | ✅ Токен | ✅ Токен |
| **Связи M:N** |
| `/api/v1/person-event/` | ❌ Публичный | ✅ Токен | - | - | ✅ Токен |
| `/api/v1/person-keyword/` | ❌ Публичный | ✅ Токен | - | - | ✅ Токен |
| `/api/v1/event-keyword/` | ❌ Публичный | ✅ Токен | - | - | ✅ Токен |
| `/api/v1/person-book/` | ❌ Публичный | ✅ Токен | - | - | ✅ Токен |
| `/api/v1/event-book/` | ❌ Публичный | ✅ Токен | - | - | ✅ Токен |
| `/api/v1/person-profession/` | ❌ Публичный | ✅ Токен | - | - | ✅ Токен |

**Легенда:**
- ❌ Публичный — не требует токена
- ✅ Токен — требуется `Authorization: Token ...`
- `-` — метод не используется для связей

---

## 💻 Примеры кода для Next.js

### Полная библиотека API

```typescript
// app/lib/api.ts

const API_URL = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:8000'
export const API_BASE = `${API_URL}/api/v1`

// ===============================
// АУТЕНТИФИКАЦИЯ
// ===============================

export async function login(username: string, password: string) {
  const response = await fetch(`${API_BASE}/auth/login/`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ username, password }),
  })
  
  if (!response.ok) {
    throw new Error('Login failed')
  }
  
  const data = await response.json()
  localStorage.setItem('authToken', data.token)
  return data.token
}

export function logout() {
  localStorage.removeItem('authToken')
}

export function getToken(): string | null {
  return localStorage.getItem('authToken')
}

function getAuthHeaders() {
  const token = getToken()
  return token ? { 'Authorization': `Token ${token}` } : {}
}

// ===============================
// СОБЫТИЯ
// ===============================

export async function getEvents(params?: {
  page?: number
  search?: string
  date?: string
  street?: number
  ordering?: string
}) {
  const queryParams = new URLSearchParams()
  
  if (params?.page) queryParams.append('page', params.page.toString())
  if (params?.search) queryParams.append('search', params.search)
  if (params?.date) queryParams.append('date', params.date)
  if (params?.street) queryParams.append('street', params.street.toString())
  if (params?.ordering) queryParams.append('ordering', params.ordering)
  
  const url = `${API_BASE}/event/?${queryParams}`
  const response = await fetch(url)
  
  if (!response.ok) {
    throw new Error(`API error: ${response.status}`)
  }
  
  return response.json()
}

export async function getEvent(id: number) {
  const response = await fetch(`${API_BASE}/event/${id}/`)
  
  if (!response.ok) {
    throw new Error(`API error: ${response.status}`)
  }
  
  return response.json()
}

export async function createEvent(eventData: {
  title: string
  date: string
  description_html: string
  street?: number | null
}) {
  const response = await fetch(`${API_BASE}/event/`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      ...getAuthHeaders(),  // ← Добавляет токен
    },
    body: JSON.stringify(eventData),
  })
  
  if (!response.ok) {
    throw new Error('Failed to create event')
  }
  
  return response.json()
}

export async function updateEvent(
  id: number,
  eventData: Partial<{
    title: string
    date: string
    description_html: string
    street: number | null
  }>
) {
  const response = await fetch(`${API_BASE}/event/${id}/`, {
    method: 'PATCH',
    headers: {
      'Content-Type': 'application/json',
      ...getAuthHeaders(),  // ← Добавляет токен
    },
    body: JSON.stringify(eventData),
  })
  
  if (!response.ok) {
    throw new Error('Failed to update event')
  }
  
  return response.json()
}

export async function deleteEvent(id: number) {
  const response = await fetch(`${API_BASE}/event/${id}/`, {
    method: 'DELETE',
    headers: getAuthHeaders(),  // ← Добавляет токен
  })
  
  if (!response.ok) {
    throw new Error('Failed to delete event')
  }
  
  return response.ok
}

// ===============================
// ПЕРСОНЫ
// ===============================

export async function getPersons(params?: {
  page?: number
  search?: string
  full_text?: string
  ordering?: string
}) {
  const queryParams = new URLSearchParams()
  
  if (params?.page) queryParams.append('page', params.page.toString())
  if (params?.search) queryParams.append('search', params.search)
  if (params?.full_text) queryParams.append('full_text', params.full_text)
  if (params?.ordering) queryParams.append('ordering', params.ordering)
  
  const url = `${API_BASE}/person/?${queryParams}`
  const response = await fetch(url)
  
  return response.json()
}

export async function getPerson(id: number) {
  const response = await fetch(`${API_BASE}/person/${id}/`)
  return response.json()
}

export async function createPerson(personData: any) {
  const response = await fetch(`${API_BASE}/person/`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      ...getAuthHeaders(),
    },
    body: JSON.stringify(personData),
  })
  
  return response.json()
}

// ===============================
// СПРАВОЧНИКИ
// ===============================

export async function getStreets() {
  const response = await fetch(`${API_BASE}/street/`)
  return response.json()
}

export async function getKeywords() {
  const response = await fetch(`${API_BASE}/keyword/`)
  return response.json()
}

export async function getProfessions() {
  const response = await fetch(`${API_BASE}/profession/`)
  return response.json()
}

export async function getBooks() {
  const response = await fetch(`${API_BASE}/book/`)
  return response.json()
}

// ===============================
// МЕДИА
// ===============================

export function getImageUrl(imagePath: string | null): string {
  if (!imagePath) return '/images/placeholder.jpg'
  return `${API_URL}/media/${imagePath}`
}
```

---

### Пример использования в компоненте

```tsx
// app/admin/events/create/page.tsx
'use client'

import { useState } from 'react'
import { useRouter } from 'next/navigation'
import { createEvent } from '@/lib/api'

export default function CreateEventPage() {
  const [title, setTitle] = useState('')
  const [date, setDate] = useState('')
  const [description, setDescription] = useState('')
  const [error, setError] = useState('')
  const router = useRouter()
  
  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    setError('')
    
    try {
      await createEvent({
        title,
        date,
        description_html: `<p>${description}</p>`,
      })
      
      router.push('/admin/events')
    } catch (err) {
      setError('Ошибка создания события. Проверьте токен.')
    }
  }
  
  return (
    <form onSubmit={handleSubmit}>
      <h1>Создать событие</h1>
      
      {error && <div className="error">{error}</div>}
      
      <input
        type="text"
        placeholder="Название"
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        required
      />
      
      <input
        type="date"
        value={date}
        onChange={(e) => setDate(e.target.value)}
        required
      />
      
      <textarea
        placeholder="Описание"
        value={description}
        onChange={(e) => setDescription(e.target.value)}
        required
      />
      
      <button type="submit">Создать</button>
    </form>
  )
}
```

---

## ✅ Итого

**Для чтения данных (GET):**
```typescript
const events = await getEvents()  // Токен НЕ нужен
```

**Для изменения данных (POST/PUT/PATCH/DELETE):**
```typescript
// 1. Сначала залогиниться
await login('admin', 'password')

// 2. Токен сохранился в localStorage
// 3. Теперь можно изменять данные
await createEvent({ title: 'Новое событие', date: '2024-01-01' })
```

🚀 **Готово! API полностью документировано с аутентификацией.**




## 📊 Сводная таблица всех маршрутов и endpoints

### HTML интерфейс

| Маршрут | Endpoint | Метод | Описание |
|---------|----------|-------|----------|
| `''` | `/` | GET | Главная (события на сегодня) |
| `'events/'` | `/events/` | GET | Список событий |
| `'events/date=<date>'` | `/events/date=2024-01-01` | GET | События на дату |
| `'events/title=<title>'` | `/events/title=памятник` | GET | Поиск по названию |
| `'events/keyword=<id>'` | `/events/keyword=7` | GET | Фильтр по тегу |
| `'events/street=<id>'` | `/events/street=175` | GET | Фильтр по улице |
| `'event/<int:id>'` | `/event/394` | GET | Детали события |
| `'persons/'` | `/persons/` | GET | Список персон |
| `'persons/letter=<letter>'` | `/persons/letter=А` | GET | Персоны на букву |
| `'persons/name=<name>'` | `/persons/name=Абрамов` | GET | Поиск по ФИО |
| `'persons/profession=<id>'` | `/persons/profession=1` | GET | Фильтр по профессии |
| `'person/<int:id>'` | `/person/210` | GET | Детали персоны |

**Итого: 26 HTML маршрутов → бесконечное количество endpoints**

---

### REST API v1

| Ресурс | Маршрут роутера | Endpoints | Методы |
|--------|-----------------|-----------|--------|
| **События** | `r'event'` | `/api/v1/event/`<br>`/api/v1/event/{id}/` | GET, POST<br>GET, PUT, PATCH, DELETE |
| **Персоны** | `r'person'` | `/api/v1/person/`<br>`/api/v1/person/{id}/` | GET, POST<br>GET, PUT, PATCH, DELETE |
| **Улицы** | `r'street'` | `/api/v1/street/`<br>`/api/v1/street/{id}/` | GET, POST<br>GET, PUT, PATCH, DELETE |
| **Теги** | `r'keyword'` | `/api/v1/keyword/`<br>`/api/v1/keyword/{id}/` | GET, POST<br>GET, PUT, PATCH, DELETE |
| **Профессии** | `r'profession'` | `/api/v1/profession/`<br>`/api/v1/profession/{id}/` | GET, POST<br>GET, PUT, PATCH, DELETE |
| **Книги** | `r'book'` | `/api/v1/book/`<br>`/api/v1/book/{id}/` | GET, POST<br>GET, PUT, PATCH, DELETE |
| **Связи** | `r'person-event'` | `/api/v1/person-event/`<br>`/api/v1/person-event/{id}/` | GET, POST<br>GET, DELETE |
| | `r'person-keyword'` | `/api/v1/person-keyword/`<br>`/api/v1/person-keyword/{id}/` | GET, POST<br>GET, DELETE |
| | `r'event-keyword'` | `/api/v1/event-keyword/`<br>`/api/v1/event-keyword/{id}/` | GET, POST<br>GET, DELETE |
| | `r'person-book'` | `/api/v1/person-book/`<br>`/api/v1/person-book/{id}/` | GET, POST<br>GET, DELETE |
| | `r'event-book'` | `/api/v1/event-book/`<br>`/api/v1/event-book/{id}/` | GET, POST<br>GET, DELETE |
| | `r'person-profession'` | `/api/v1/person-profession/`<br>`/api/v1/person-profession/{id}/` | GET, POST<br>GET, DELETE |

**Итого: 12 ViewSets → 24 API endpoints**

---

## 📖 Глоссарий терминов

### Маршрут (Route)
**Определение в коде** паттерна URL, который Django использует для сопоставления запросов

**Пример:**
```python
path('event/<int:id>', event, name='event_view')
```

---

### Endpoint (Конечная точка)
**Конкретный URL**, по которому доступен ресурс

**Пример:**
```
http://127.0.0.1:8000/event/394
```

---

### Query параметры
Параметры после знака `?` в URL

**Пример:**
```
/api/v1/event/?page=2&ordering=-date
              ↑
              query параметры
```

---

### Path параметры
Параметры, встроенные в путь URL

**Пример:**
```
/event/394
       ↑
       path параметр (id=394)
```

---

### ViewSet
Класс Django REST Framework, автоматически создающий CRUD endpoints

---

### CRUD
Create, Read, Update, Delete — базовые операции с данными

---

## ✅ Итоговая статистика

```
┌───────────────────────┬──────────┐
│ Тип                   │ Количество│
├───────────────────────┼──────────┤
│ HTML маршрутов        │ 26       │
│ REST API ViewSets     │ 12       │
│ REST API endpoints    │ 24       │
│ Всего маршрутов       │ 38       │
├───────────────────────┼──────────┤
│ События в БД          │ 195      │
│ Персоны в БД          │ 208      │
│ Улицы                 │ 3        │
│ Профессии             │ 3        │
│ Книги                 │ 3        │
│ Ключевые слова        │ 2        │
└───────────────────────┴──────────┘
```

---












## 1️⃣ HTML ИНТЕРФЕЙС (Django Templates)

### 🏠 Главная страница

#### Маршрут:
```python
path('', events, name='events_view')
```

#### Endpoint:
```
GET http://127.0.0.1:8000/
```

#### Описание:
Главная страница сайта — календарь событий на сегодняшний день

#### Параметры:
- Нет

#### Возвращает:
HTML страница с календарем и списком событий на сегодня

#### Пример использования:
```
Браузер: http://127.0.0.1:8000/
→ Показывает события, произошедшие 26 января
```

---

### 📅 События

#### 1. Список всех событий

**Маршрут:**
```python
path('events/', events, name='events_view')
```

**Endpoint:**
```
GET http://127.0.0.1:8000/events/
```

**Описание:** Список всех событий с календарем

**Параметры URL:** Нет

**Query параметры:**
- `?page=2` — номер страницы (пагинация)

**Возвращает:** HTML страница со списком событий (12 на странице)

**Пример:**
```
http://127.0.0.1:8000/events/
http://127.0.0.1:8000/events/?page=2
```

---

#### 2. События на конкретную дату

**Маршрут:**
```python
path('events/date=<date>', events, name='events_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/events/date=2024-01-01
GET http://127.0.0.1:8000/events/date=1983-01-01
GET http://127.0.0.1:8000/events/date=1705-05-27
```

**Описание:** События, произошедшие в указанную дату

**Параметры:**
- `<date>` — дата в формате `YYYY-MM-DD`

**Возвращает:** HTML страница с событиями на указанную дату

**Особенность:** Если нет событий на конкретный день, показывает все события за месяц

---

#### 3. Поиск событий по названию

**Маршрут:**
```python
path('events/title=<title>', events, name='events_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/events/title=памятник
GET http://127.0.0.1:8000/events/title=библиотека
GET http://127.0.0.1:8000/events/title=Попков
```

**Описание:** Поиск событий по тексту в названии

**Параметры:**
- `<title>` — текст для поиска (регистронезависимый)

**Возвращает:** HTML страница с результатами поиска

---

#### 4. POST обработка формы поиска по дате

**Маршрут:**
```python
path('events/get_date', get_date_events, name='get_date_events')
```

**Endpoint:**
```
POST http://127.0.0.1:8000/events/get_date
```

**Описание:** Обработка формы поиска по дате

**Тело запроса:**
```
search_date=2024-01-01
```

**Возвращает:** Редирект на `/events/date=2024-01-01`

---

#### 5. POST обработка формы поиска по названию

**Маршрут:**
```python
path('events/get_title', get_title_events, name='get_title_events')
```

**Endpoint:**
```
POST http://127.0.0.1:8000/events/get_title
```

**Описание:** Обработка формы поиска по названию

**Тело запроса:**
```
search_title=памятник
```

**Возвращает:** Редирект на `/events/title=памятник`

---

#### 6. Список ключевых слов событий

**Маршрут:**
```python
path('events/keywords', events_keywords, name='events_keywords_view')
```

**Endpoint:**
```
GET http://127.0.0.1:8000/events/keywords
```

**Описание:** Алфавитный указатель ключевых слов, связанных с событиями

**Возвращает:** HTML страница с алфавитным списком тегов

---

#### 7. Фильтр событий по ключевому слову

**Маршрут:**
```python
path('events/keyword=<int:keyword_id>', events, name='events_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/events/keyword=7
GET http://127.0.0.1:8000/events/keyword=8
```

**Описание:** События, отмеченные конкретным ключевым словом

**Параметры:**
- `<keyword_id>` — ID ключевого слова

**Возвращает:** HTML страница с отфильтрованными событиями

---

#### 8. Список улиц событий

**Маршрут:**
```python
path('events/streets', events_streets, name='events_streets_view')
```

**Endpoint:**
```
GET http://127.0.0.1:8000/events/streets
```

**Описание:** Алфавитный указатель улиц, на которых происходили события

**Возвращает:** HTML страница со списком улиц

---

#### 9. Фильтр событий по улице

**Маршрут:**
```python
path('events/street=<int:street_id>', events, name='events_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/events/street=175
GET http://127.0.0.1:8000/events/street=176
GET http://127.0.0.1:8000/events/street=177
```

**Описание:** События, произошедшие на конкретной улице

**Параметры:**
- `<street_id>` — ID улицы
  - 175 = Каменноостровский проспект
  - 176 = Мичуринская улица
  - 177 = Кронверкский проспект

**Возвращает:** HTML страница с событиями на улице

---

#### 10. Навигация по месяцам календаря

**Маршруты:**
```python
path('events/previous_month_from_<date>', get_previous_month, name='previous_month_calendar_view')
path('events/next_month_from_<date>', get_next_month, name='next_month_calendar_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/events/previous_month_from_2024-01-15
GET http://127.0.0.1:8000/events/next_month_from_2024-01-15
```

**Описание:** Навигация по календарю (переход на предыдущий/следующий месяц)

**Параметры:**
- `<date>` — текущая дата

**Возвращает:** Редирект на первое число предыдущего/следующего месяца

**Пример:**
```
previous_month_from_2024-01-15 → редирект на /events/date=2023-12-01
next_month_from_2024-01-15     → редирект на /events/date=2024-02-01
```

---

#### 11. Детальная страница события

**Маршрут:**
```python
path('event/<int:id>', event, name='event_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/event/394
GET http://127.0.0.1:8000/event/395
GET http://127.0.0.1:8000/event/396
GET http://127.0.0.1:8000/event/401
```

**Описание:** Полная информация о событии

**Параметры:**
- `<id>` — ID события (194 события доступны)

**Возвращает:** HTML страница с:
- Название события
- Дата
- Описание (HTML)
- Изображение
- Связанные книги
- Связанные ключевые слова

**Пример данных:**
```
ID: 394
Название: "Открыта мемориальная доска П.С. Попкову"
Дата: 1983-01-01
```

---

### 👥 Персоны

#### 1. Список всех персон

**Маршрут:**
```python
path('persons/', persons, name='persons_view')
```

**Endpoint:**
```
GET http://127.0.0.1:8000/persons/
```

**Описание:** Список всех персон

**Query параметры:**
- `?page=2` — номер страницы

**Возвращает:** HTML страница со списком персон (12 на странице)

---

#### 2. Фильтр персон по первой букве фамилии

**Маршрут:**
```python
path('persons/letter=<str:letter>', persons, name='persons_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/persons/letter=А
GET http://127.0.0.1:8000/persons/letter=Б
GET http://127.0.0.1:8000/persons/letter=П
```

**Описание:** Персоны, фамилия которых начинается на указанную букву

**Параметры:**
- `<letter>` — буква русского алфавита (А-Я)

**Возвращает:** HTML страница с отфильтрованными персонами

**Пример:**
```
letter=А → Абрамов, Агнивцев, Алексеева, Алфёров
```

---

#### 3. Поиск персон по ФИО

**Маршрут:**
```python
path('persons/name=<name>', persons, name='persons_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/persons/name=Абрамов
GET http://127.0.0.1:8000/persons/name=Фёдор
GET http://127.0.0.1:8000/persons/name=Абрамов Фёдор
```

**Описание:** Поиск по имени, фамилии или отчеству

**Параметры:**
- `<name>` — текст для поиска (может быть несколько слов)

**Логика поиска:**
- Разбивает запрос на слова
- Ищет каждое слово в ФИО (фамилия, имя, отчество)
- Все слова должны присутствовать (логическое AND)

**Пример:**
```
name=Абрамов Фёдор
→ найдет: "Абрамов Фёдор Александрович"
→ не найдет: "Абрамов Иван Иванович" (нет слова "Фёдор")
```

---

#### 4. POST обработка формы поиска по имени

**Маршрут:**
```python
path('persons/get_name', get_name_persons, name='get_name_persons')
```

**Endpoint:**
```
POST http://127.0.0.1:8000/persons/get_name
```

**Тело запроса:**
```
search_name=Абрамов
```

**Возвращает:** Редирект на `/persons/name=Абрамов`

---

#### 5. Список профессий

**Маршрут:**
```python
path('persons/professions', persons_professions, name='persons_professions_view')
```

**Endpoint:**
```
GET http://127.0.0.1:8000/persons/professions
```

**Описание:** Алфавитный указатель профессий

**Возвращает:** HTML страница со списком профессий (Литературовед, Писатель, Публицист)

---

#### 6. Фильтр персон по профессии

**Маршрут:**
```python
path('persons/profession=<int:profession_id>', persons, name='persons_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/persons/profession=1  (Литературовед)
GET http://127.0.0.1:8000/persons/profession=2  (Писатель)
GET http://127.0.0.1:8000/persons/profession=6  (Публицист)
```

**Описание:** Персоны с указанной профессией

**Параметры:**
- `<profession_id>` — ID профессии

---

#### 7. Список ключевых слов персон

**Маршрут:**
```python
path('persons/keywords', persons_keywords, name='persons_keywords_view')
```

**Endpoint:**
```
GET http://127.0.0.1:8000/persons/keywords
```

**Описание:** Алфавитный указатель тегов персон

---

#### 8. Фильтр персон по ключевому слову

**Маршрут:**
```python
path('persons/keyword=<int:keyword_id>', persons, name='persons_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/persons/keyword=7
GET http://127.0.0.1:8000/persons/keyword=8
```

**Описание:** Персоны с указанным тегом

---

#### 9. Список улиц персон

**Маршрут:**
```python
path('persons/streets', persons_streets, name='persons_streets_view')
```

**Endpoint:**
```
GET http://127.0.0.1:8000/persons/streets
```

**Описание:** Алфавитный указатель улиц, связанных с персонами

---

#### 10. Фильтр персон по улице

**Маршрут:**
```python
path('persons/street=<int:street_id>', persons, name='persons_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/persons/street=175
GET http://127.0.0.1:8000/persons/street=176
GET http://127.0.0.1:8000/persons/street=177
```

**Описание:** Персоны, связанные с указанной улицей (жили, работали, учились)

---

#### 11. Детальная страница персоны

**Маршрут:**
```python
path('person/<int:id>', person, name='person_view')
```

**Endpoints:**
```
GET http://127.0.0.1:8000/person/210  (Абрамов Ф.А.)
GET http://127.0.0.1:8000/person/211  (Агнивцев Н.Я.)
GET http://127.0.0.1:8000/person/214  (Алфёров Ж.И.)
```

**Описание:** Полная биография персоны

**Параметры:**
- `<id>` — ID персоны (208 персон доступно)

**Возвращает:** HTML страница с:
- ФИО
- Даты жизни
- Краткое описание
- Развернутая биография (статья)
- Фотография
- Связанные книги
- Профессии
- Ключевые слова
- Улицы

---




Эта документация покрывает **100% всех маршрутов и endpoints** вашего проекта! 🚀
