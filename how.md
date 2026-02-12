# Порядок работы Django приложения

## Структура файлов

```
dates_and_persons/
├── models.py          # 1️⃣ Определяет БД (таблицы, поля, связи)
├── serializers.py     # 2️⃣ Преобразует данные БД ↔ JSON
├── views.py           # 3️⃣ Обрабатывает запросы, возвращает ответы
├── urls.py            # 4️⃣ Маршрутизация (URL → view)
├── admin.py           # 5️⃣ Админ-панель Django
├── forms.py           # 6️⃣ Формы для админки
└── tests.py           # 7️⃣ Тесты
```

---

## Путь запроса (API)

```
Запрос от фронтенда (Next.js)
    ↓
1. urls.py - находит нужный роутер/view
    ↓
2. views.py - получает запрос
    ↓
3. models.py - запрос к БД (ORM)
    ↓
4. serializers.py - данные из БД → JSON
    ↓
5. views.py - отправляет JSON ответ
    ↓
Ответ на фронтенд
```

---

## Детально по файлам

### **models.py** - База данных
```python
# ЧТО: описание таблиц БД
# КОГДА: первым делом при разработке
# ПРИМЕР: Person, Event, Street классы

class Person(models.Model):
    first_name = models.CharField(...)  # → таблица в PostgreSQL/MySQL
```

### **serializers.py** - Конвертер данных
```python
# ЧТО: преобразует Python объекты ↔ JSON
# КОГДА: после models.py, перед views.py
# ПРИМЕР: PersonSerializer делает Person → JSON для API

PersonSerializer → {"id": 1, "first_name": "Иван"}
```

### **views.py** - Логика обработки
```python
# ЧТО: принимает HTTP запросы, возвращает ответы
# КОГДА: при каждом API запросе
# ПРИМЕР: 

class PersonRestFrameworkView(viewsets.ModelViewSet):
    queryset = Person.objects.all()  # ← взять данные из БД
    serializer_class = PersonSerializer  # ← превратить в JSON
```

### **urls.py** - Маршрутизация
```python
# ЧТО: связывает URL с view
# КОГДА: при входящем запросе (первым)
# ПРИМЕР:

router.register(r'person', PersonRestFrameworkView)
# GET /api/v1/person/ → PersonRestFrameworkView
```

### **admin.py** - Админ-панель
```python
# ЧТО: настройка интерфейса admin.site
# КОГДА: для ручного управления данными через /admin/
# ПРИМЕР:

admin.site.register(Person, Person_Admin)
# → можно редактировать персон через браузер
```

### **forms.py** - Формы Django
```python
# ЧТО: определяет поля форм для админки
# КОГДА: используется в admin.py
# ПРИМЕР:

class Person_Model_Form(forms.ModelForm):
    first_name = forms.CharField(widget=...)  # ← виджет в админке
```

### **tests.py** - Тесты
```python
# ЧТО: автоматическая проверка кода
# КОГДА: запускаешь вручную (python manage.py test)
# ПРИМЕР:

def test_person_creation(self):
    person = Person.objects.create(...)
    self.assertEqual(person.first_name, "Иван")
```

---

## Примеры запросов

### **GET /api/v1/person/** (список персон)
```
1. urls.py: роутер находит PersonRestFrameworkView
2. views.py: метод .list() (автоматический)
3. models.py: Person.objects.all() — выборка из БД
4. serializers.py: PersonListSerializer — краткий JSON
5. views.py: отправка [{id:1, name:"Иван"}]
```

### **GET /api/v1/person/5/** (детали персоны)
```
1. urls.py: находит PersonRestFrameworkView
2. views.py: метод .retrieve() + get_serializer_class()
3. models.py: Person.objects.get(id=5)
4. serializers.py: PersonDetailSerializer — полный JSON со связями
5. views.py: отправка {id:5, events:[...], books:[...]}
```

### **POST /api/v1/person/** (создание)
```
1. urls.py → PersonRestFrameworkView
2. views.py: метод .create()
3. serializers.py: валидация JSON
4. models.py: Person.objects.create(...)
5. serializers.py: сериализация нового объекта
6. views.py: ответ 201 Created
```

---

## Порядок разработки

```
1. models.py       → Спроектировать БД
2. admin.py        → Настроить админку (для заполнения данных)
3. forms.py        → Формы для админки
4. serializers.py  → Описать JSON структуру API
5. views.py        → Логика обработки запросов
6. urls.py         → Подключить роутинг
7. tests.py        → Написать тесты
```

---

## Что происходит прямо сейчас у вас

```
Next.js (фронтенд)
    ↓ fetch('/api/v1/person/')
Django urls.py → router → PersonRestFrameworkView
    ↓
views.py: queryset = Person.objects.all()
    ↓
models.py: запрос к PostgreSQL/SQLite
    ↓
serializers.py: PersonListSerializer(data) → JSON
    ↓
HTTP Response: [{"id":1, "full_name":"Пушкин А.С."}]
    ↓
Next.js получает JSON и рендерит
```
