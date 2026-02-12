# 📊 ОБЗОР МОДЕЛЕЙ ВАШЕГО ПРОЕКТА

## 🗂️ СТРУКТУРА БАЗЫ ДАННЫХ

Ваш проект имеет **6 основных моделей** и **6 промежуточных моделей** для связей Many-to-Many.

---

## 1️⃣ ОСНОВНЫЕ МОДЕЛИ (Main Models)

### 📍 **Street (Улица)**

**Назначение:** Хранит информацию об улицах Петроградского района

```python
class Street(models.Model):
    name = models.CharField(max_length=100)  # Название улицы
```

**Поля:**
- `id` (автоматически) — уникальный идентификатор
- `name` — название улицы (например, "Каменноостровский проспект")

**Пример данных:**
```
id=1, name="Каменноостровский проспект"
id=2, name="Большой проспект П.С."
id=3, name="Петропавловская улица"
```

**Зачем:** Географическая привязка событий и персон

---

### 👔 **Profession (Профессия)**

**Назначение:** Справочник профессий для персон

```python
class Profession(models.Model):
    name = models.CharField(max_length=100)  # Название профессии
```

**Поля:**
- `id` — уникальный идентификатор
- `name` — название профессии (например, "Писатель", "Художник")

**Пример данных:**
```
id=1, name="Писатель"
id=2, name="Государственный деятель"
id=3, name="Художник"
```

**Зачем:** Классификация персон по роду деятельности

---

### 📚 **Book (Книга)**

**Назначение:** Литературные источники о персонах и событиях

```python
class Book(models.Model):
    author = models.CharField(max_length=100)     # Автор книги
    title = models.CharField(max_length=100)      # Название книги
    url = models.CharField(max_length=1000)       # Ссылка на книгу
    image = models.ImageField(...)                # Обложка книги
```

**Поля:**
- `id` — уникальный идентификатор
- `author` — автор (например, "Иванов И.И.")
- `title` — название (например, "История Петроградского района")
- `url` — ссылка на книгу онлайн
- `image` — изображение обложки

**Пример данных:**
```
id=1, author="Иванов И.И.", title="История Петроградского района", url="http://..."
id=2, author="Петрова А.А.", title="Жизнь замечательных людей", url="http://..."
```

**Зачем:** Ссылки на источники информации

---

### 📅 **Event (Событие)**

**Назначение:** Исторические события Петроградского района

```python
class Event(models.Model):
    title = models.CharField(max_length=100)          # Название события
    date = models.DateField()                          # Дата события
    street = models.ForeignKey(Street, ...)           # Связь с улицей
    description_html = models.CharField(max_length=10000)  # Описание (HTML)
    image = models.ImageField(...)                    # Фотография события
```

**Поля:**
- `id` — уникальный идентификатор
- `title` — название события
- `date` — точная дата события (YYYY-MM-DD)
- `street` — улица, где произошло событие (ForeignKey → Street)
- `description_html` — полное описание события (может содержать HTML)
- `image` — изображение события

**Пример данных:**
```
id=394, 
title="Открыта мемориальная доска П.С. Попкову", 
date="1983-01-01",
street=Street(id=1),
description_html="<p>В 1983 году на доме...</p>",
image="events_images/event_394.jpg"
```

**Зачем:** Основная сущность — памятные даты района

---

### 👤 **Person (Персона)**

**Назначение:** Биографии выдающихся людей, связанных с районом

```python
class Person(models.Model):
    last_name = models.CharField(max_length=100)      # Фамилия
    first_name = models.CharField(max_length=100)     # Имя
    middle_name = models.CharField(max_length=100)    # Отчество
    birth_date = models.DateField(...)                # Дата рождения
    death_date = models.DateField(...)                # Дата смерти
    description_html = models.CharField(max_length=10000)  # Краткое описание
    article_html = models.CharField(max_length=10000)      # Полная статья
    image = models.ImageField(...)                    # Фотография персоны
```

**Поля:**
- `id` — уникальный идентификатор
- `last_name` — фамилия (например, "Абрамов")
- `first_name` — имя (например, "Фёдор")
- `middle_name` — отчество (например, "Александрович")
- `birth_date` — дата рождения
- `death_date` — дата смерти (может быть пустой для здравствующих)
- `description_html` — краткая биография (HTML)
- `article_html` — полная статья о персоне (HTML)
- `image` — фотография

**Пример данных:**
```
id=210,
last_name="Абрамов",
first_name="Фёдор",
middle_name="Александрович",
birth_date="1920-02-29",
death_date="1983-05-14",
description_html="<p>Писатель, литературовед...</p>",
article_html="<p>Родился в деревне...</p>",
image="persons_images/person_210.jpg"
```

**Зачем:** Биографическая информация о выдающихся личностях

---

### 🏷️ **Keyword (Ключевое слово / Тег)**

**Назначение:** Теги для классификации событий и персон

```python
class Keyword(models.Model):
    keyword = models.CharField(max_length=100, unique=True)  # Тег
```

**Поля:**
- `id` — уникальный идентификатор
- `keyword` — ключевое слово (уникальное)

**Пример данных:**
```
id=1, keyword="Литература"
id=2, keyword="Государственные деятели"
id=3, keyword="Искусство"
```

**Зачем:** Тематическая классификация контента

---

## 2️⃣ ПРОМЕЖУТОЧНЫЕ МОДЕЛИ (Many-to-Many Relations)

Эти модели создают **связи между основными моделями**.

### 🔗 **Person_event** (Персона ↔ Событие)

**Назначение:** Связывает персон с событиями

```python
class Person_event(models.Model):
    person_id = models.ForeignKey(Person, ...)  # Персона
    event_id = models.ForeignKey(Event, ...)    # Событие
```

**Что хранит:**
```
person_id=210, event_id=394  # Абрамов Ф.А. участвовал в событии #394
person_id=211, event_id=394  # Попков П.С. участвовал в событии #394
```

**Зачем:** Одно событие может быть связано с несколькими персонами, и наоборот

**Пример:**
- Событие "Открытие библиотеки" → участвовали: Персона1, Персона2, Персона3
- Персона "Абрамов Ф.А." → участвовал в: Событие1, Событие2, Событие3

---

### 🏷️ **Person_keyword** (Персона ↔ Тег)

**Назначение:** Связывает персон с ключевыми словами

```python
class Person_keyword(models.Model):
    keyword_id = models.ForeignKey(Keyword, ...)  # Тег
    person_id = models.ForeignKey(Person, ...)    # Персона
```

**Пример:**
```
keyword_id=1 ("Литература"), person_id=210 (Абрамов)
keyword_id=1 ("Литература"), person_id=215 (Пушкин)
```

**Зачем:** Тематическая классификация персон

---

### 🏷️ **Event_keyword** (Событие ↔ Тег)

**Назначение:** Связывает события с ключевыми словами

```python
class Event_keyword(models.Model):
    keyword_id = models.ForeignKey(Keyword, ...)  # Тег
    event_id = models.ForeignKey(Event, ...)      # Событие
```

**Пример:**
```
keyword_id=2 ("Культура"), event_id=394
```

**Зачем:** Тематическая классификация событий

---

### 📚 **Person_book** (Персона ↔ Книга)

**Назначение:** Связывает персон с книгами (источники информации о персоне)

```python
class Person_book(models.Model):
    book_id = models.ForeignKey(Book, ...)     # Книга
    person_id = models.ForeignKey(Person, ...) # Персона
```

**Пример:**
```
book_id=1, person_id=210  # О Абрамове написано в книге #1
book_id=2, person_id=210  # О Абрамове написано в книге #2
```

**Зачем:** Указать, в каких книгах можно прочитать о персоне

---

### 📚 **Event_book** (Событие ↔ Книга)

**Назначение:** Связывает события с книгами (источники информации о событии)

```python
class Event_book(models.Model):
    book_id = models.ForeignKey(Book, ...)    # Книга
    event_id = models.ForeignKey(Event, ...)  # Событие
```

**Пример:**
```
book_id=1, event_id=394  # О событии #394 написано в книге #1
```

**Зачем:** Указать источники информации о событии

---

### 👔 **Person_profession** (Персона ↔ Профессия)

**Назначение:** Связывает персон с профессиями

```python
class Person_profession(models.Model):
    profession_id = models.ForeignKey(Profession, ...)  # Профессия
    person_id = models.ForeignKey(Person, ...)          # Персона
```

**Пример:**
```
profession_id=1 ("Писатель"), person_id=210 (Абрамов)
profession_id=2 ("Литературовед"), person_id=210 (Абрамов)
```

**Зачем:** Одна персона может иметь несколько профессий

---

### 📍 **Person_street** (Персона ↔ Улица)

**Назначение:** Связывает персон с улицами (где жили/работали)

```python
class Person_street(models.Model):
    person_id = models.ForeignKey(Person, ...)  # Персона
    street_id = models.ForeignKey(Street, ...)  # Улица
```

**Пример:**
```
person_id=210, street_id=1  # Абрамов жил на Каменноостровском пр.
person_id=210, street_id=2  # Абрамов также работал на Большом пр.
```

**Зачем:** Географическая привязка персон (одна персона может быть связана с несколькими улицами)

---

## 📊 ER-ДИАГРАММА (Схема связей)

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│   PERSON    │────M:N──│ Person_event │──N:M────│    EVENT    │
│  (Персона)  │         └──────────────┘         │  (Событие)  │
└─────┬───────┘                                  └──────┬──────┘
      │                                                 │
      │ M:N (Person_keyword)                           │ M:N (Event_keyword)
      │                                                 │
      ├─────────────┐                         ┌────────┤
      │             │                         │        │
      ▼             ▼                         ▼        ▼
┌──────────┐  ┌──────────┐           ┌──────────┐  ┌────────┐
│ KEYWORD  │  │   BOOK   │           │   BOOK   │  │ STREET │
│  (Тег)   │  │ (Книга)  │           │ (Книга)  │  │(Улица) │
└──────────┘  └──────────┘           └──────────┘  └────────┘
      ▲             ▲                                    ▲
      │             │                                    │
      │ M:N         │ M:N                                │ 1:N
      │             │                                    │
┌─────┴───────┐     └──────────────┐            ┌───────┴────────┐
│  PERSON     │                    │            │     EVENT      │
│  (Персона)  │────M:N────────────┐│            │   (Событие)    │
└─────┬───────┘    (Person_book)  ││            └────────────────┘
      │                            ││
      │ M:N (Person_profession)    ││
      │                            ││
      ▼                            ▼│
┌──────────────┐           ┌───────┴┴────┐
│  PROFESSION  │           │    BOOK     │
│ (Профессия)  │           │   (Книга)   │
└──────────────┘           └─────────────┘
      ▲
      │ M:N (Person_street)
      │
┌─────┴───────┐
│   PERSON    │
│  (Персона)  │
└─────┬───────┘
      │ M:N
      ▼
┌──────────┐
│  STREET  │
│ (Улица)  │
└──────────┘
```

---

## 🎯 ИТОГОВАЯ ТАБЛИЦА МОДЕЛЕЙ

| Модель | Тип | Назначение | Количество записей |
|--------|-----|------------|-------------------|
| **Street** | Основная | Улицы района | ~3 |
| **Profession** | Основная | Профессии | ~3 |
| **Book** | Основная | Литературные источники | ~3 |
| **Event** | Основная | Исторические события | ~195 |
| **Person** | Основная | Биографии персон | ~208 |
| **Keyword** | Основная | Теги для классификации | ~2 |
| **Person_event** | Связь M:N | Персона ↔ Событие | Много |
| **Person_keyword** | Связь M:N | Персона ↔ Тег | Много |
| **Event_keyword** | Связь M:N | Событие ↔ Тег | Много |
| **Person_book** | Связь M:N | Персона ↔ Книга | Много |
| **Event_book** | Связь M:N | Событие ↔ Книга | Много |
| **Person_profession** | Связь M:N | Персона ↔ Профессия | Много |
| **Person_street** | Связь M:N | Персона ↔ Улица | Много |

---

## 🔍 ПРИМЕРЫ ЗАПРОСОВ К МОДЕЛЯМ

### 1. Получить все события:
```python
events = Event.objects.all()
```

### 2. Получить события за январь:
```python
january_events = Event.objects.filter(date__month=1)
```

### 3. Получить персон с профессией "Писатель":
```python
writers = Person.objects.filter(
    person__profession__profession_id__name="Писатель"
)
```

### 4. Получить все события, связанные с персоной:
```python
person = Person.objects.get(id=210)
person_events = Event.objects.filter(
    person_event__person_id=person
)
```

### 5. Получить книги о персоне:
```python
person = Person.objects.get(id=210)
books = Book.objects.filter(
    person_book__person_id=person
)
```

---

## ⚠️ ЗАМЕЧАНИЯ ПО СТРУКТУРЕ

### 🔴 Проблемы в текущей структуре:

1. **Промежуточные модели созданы вручную** вместо `ManyToManyField`
   - Сложнее работать
   - Больше кода
   
2. **Названия полей `*_id`** — избыточны
   - Django автоматически добавляет `_id` к ForeignKey
   - Лучше: `person` вместо `person_id`

3. **Отсутствует поле `day` и `month` в Event**
   - Есть только `date`
   - Для календаря придется вычислять

### ✅ Рекомендации для улучшения:

**Вместо:**
```python
class Person_event(models.Model):
    person_id = models.ForeignKey(Person, ...)
    event_id = models.ForeignKey(Event, ...)
```

**Лучше использовать:**
```python
class Event(models.Model):
    # ... другие поля
    persons = models.ManyToManyField(Person, related_name='events')
```

Django автоматически создаст промежуточную таблицу!

---

## 🎓 ВЫВОДЫ

Ваша база данных описывает:
- ✅ События Петроградского района
- ✅ Выдающихся людей района
- ✅ Географическую привязку (улицы)
- ✅ Литературные источники
- ✅ Тематическую классификацию

Все модели связаны между собой через отношения Many-to-Many, что позволяет:
- Одно событие → много персон
- Одна персона → много событий
- Одна персона → много профессий
- Одна персона → много улиц
- И так далее

Это мощная и гибкая структура для краеведческой системы! 🎉



Отличные вопросы! Давайте разберем каждый пункт подробно:

---

## 1️⃣ **ПРОМЕЖУТОЧНЫЕ ТАБЛИЦЫ: Вручную vs ManyToManyField**

### 🤔 **Когда создавать промежуточную таблицу ВРУЧНУЮ:**

Ручная промежуточная таблица нужна, когда вы хотите **добавить дополнительные поля** к связи.

#### ✅ **ХОРОШИЙ ПРИМЕР — ручная таблица НУЖНА:**

```python
# Промежуточная таблица с ДОПОЛНИТЕЛЬНЫМИ полями
class Person_event(models.Model):
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    event = models.ForeignKey(Event, on_delete=models.CASCADE)
    
    # ДОПОЛНИТЕЛЬНЫЕ ПОЛЯ:
    role = models.CharField(max_length=100)  # Роль в событии ("организатор", "участник")
    date_joined = models.DateField()          # Когда присоединился
    is_main_figure = models.BooleanField()    # Главное действующее лицо?
    notes = models.TextField()                # Примечания

# В модели Event указываем промежуточную таблицу:
class Event(models.Model):
    title = models.CharField(max_length=100)
    persons = models.ManyToManyField(
        Person, 
        through='Person_event',  # ← Используем нашу таблицу
        related_name='events'
    )
```

**Польза:** Можно сохранить, что Пушкин был "главным организатором" открытия библиотеки, а Достоевский "почетным гостем".

---

### ❌ **ПЛОХОЙ ПРИМЕР — ручная таблица НЕ НУЖНА:**

```python
# Промежуточная таблица БЕЗ дополнительных полей
class Person_event(models.Model):
    person_id = models.ForeignKey(Person, on_delete=models.CASCADE)
    event_id = models.ForeignKey(Event, on_delete=models.CASCADE)
    
    # НЕТ ДОПОЛНИТЕЛЬНЫХ ПОЛЕЙ!
```

**Проблема:** Это избыточно! Django создаст такую таблицу автоматически.

**Лучше использовать:**

```python
class Event(models.Model):
    title = models.CharField(max_length=100)
    persons = models.ManyToManyField(Person, related_name='events')  # ← Всё!
```

Django автоматически создаст таблицу `dates_event_persons` с полями `event_id` и `person_id`.

---

### 📊 **СРАВНЕНИЕ:**

| Критерий | ManyToManyField | Ручная промежуточная таблица |
|----------|-----------------|------------------------------|
| **Код** | 1 строка | 5+ строк |
| **Работа** | `event.persons.add(person)` | `Person_event.objects.create(...)` |
| **Запросы** | `event.persons.all()` | `Person.objects.filter(person_event__event=event)` |
| **Доп. поля** | ❌ Нельзя | ✅ Можно |
| **Админка** | ✅ Автоматически | ⚠️ Нужно настраивать |

---

### 🎯 **ДЛЯ ВАШЕГО ПРОЕКТА:**

**Ваши промежуточные таблицы НЕ имеют дополнительных полей** → используйте `ManyToManyField`!

**Было:**
```python
class Person_event(models.Model):
    person_id = models.ForeignKey(Person, on_delete=models.CASCADE)
    event_id = models.ForeignKey(Event, on_delete=models.CASCADE)

class Person_keyword(models.Model):
    keyword_id = models.ForeignKey(Keyword, on_delete=models.CASCADE)
    person_id = models.ForeignKey(Person, on_delete=models.CASCADE)
```

**Должно быть:**
```python
class Event(models.Model):
    title = models.CharField(max_length=100)
    date = models.DateField()
    persons = models.ManyToManyField(Person, related_name='events')
    keywords = models.ManyToManyField(Keyword, related_name='events')

class Person(models.Model):
    first_name = models.CharField(max_length=100)
    professions = models.ManyToManyField(Profession, related_name='persons')
    streets = models.ManyToManyField(Street, related_name='persons')
    books = models.ManyToManyField(Book, related_name='persons')
```

---

## 2️⃣ **ПОЧЕМУ `person` ВМЕСТО `person_id`?**

### 🔴 **ПРОБЛЕМА с `person_id`:**

Django **автоматически** добавляет суффикс `_id` к ForeignKey полям в базе данных.

```python
# ❌ ПЛОХО:
class Person_event(models.Model):
    person_id = models.ForeignKey(Person, on_delete=models.CASCADE)
    event_id = models.ForeignKey(Event, on_delete=models.CASCADE)
```

**Что происходит в БД:**
```sql
CREATE TABLE person_event (
    id SERIAL PRIMARY KEY,
    person_id_id INTEGER,  -- ← ДВОЙНОЕ _id !!!
    event_id_id INTEGER    -- ← ДВОЙНОЕ _id !!!
);
```

**Как работать с кодом:**
```python
pe = Person_event.objects.get(id=1)
print(pe.person_id)      # ← Объект Person
print(pe.person_id_id)   # ← ID персоны (целое число)
```

**Путаница!** Есть `person_id` (объект) и `person_id_id` (число).

---

### ✅ **ПРАВИЛЬНО: `person` БЕЗ `_id`:**

```python
# ✅ ХОРОШО:
class Person_event(models.Model):
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    event = models.ForeignKey(Event, on_delete=models.CASCADE)
```

**Что происходит в БД:**
```sql
CREATE TABLE person_event (
    id SERIAL PRIMARY KEY,
    person_id INTEGER,  -- ← Правильно!
    event_id INTEGER    -- ← Правильно!
);
```

**Как работать с кодом:**
```python
pe = Person_event.objects.get(id=1)
print(pe.person)      # ← Объект Person
print(pe.person_id)   # ← ID персоны (целое число)
```

**Понятно!** `person` — объект, `person_id` — число.

---

### 📊 **ТАБЛИЦА СРАВНЕНИЯ:**

| Код | В Python | В SQL | Понятность |
|-----|----------|-------|-----------|
| `person_id = ForeignKey(...)` | `obj.person_id` = объект<br>`obj.person_id_id` = число | `person_id_id` | ❌ Путаница |
| `person = ForeignKey(...)` | `obj.person` = объект<br>`obj.person_id` = число | `person_id` | ✅ Понятно |

---

### 🎯 **ПРАВИЛО:**

> **Называйте ForeignKey поля БЕЗ суффикса `_id`.**  
> Django автоматически добавит `_id` в базу данных.

**Примеры правильных названий:**
```python
street = models.ForeignKey(Street, ...)       # В БД: street_id
author = models.ForeignKey(User, ...)          # В БД: author_id
parent_category = models.ForeignKey(...)       # В БД: parent_category_id
```

---

## 3️⃣ **КАК ДОБАВИТЬ `day` И `month` В EVENT?**

### 🤔 **Зачем нужны `day` и `month`?**

Для календаря нужно быстро находить события по дню/месяцу **независимо от года**:
- Все события 15 марта (любого года)
- Все события января

Если использовать только `date`, придется делать сложные SQL запросы.

---

### ✅ **РЕШЕНИЕ 1: Добавить поля `day` и `month`**

#### **A. Изменить модель:**

```python
class Event(models.Model):
    title = models.CharField(max_length=100, verbose_name='Название')
    date = models.DateField(verbose_name='Дата события')
    day = models.IntegerField(verbose_name='День')      # ← ДОБАВИТЬ
    month = models.IntegerField(verbose_name='Месяц')   # ← ДОБАВИТЬ
    description_html = models.CharField(max_length=10000, verbose_name='Статья')
    image = models.ImageField(upload_to="events_images", verbose_name='Изображение')
```

#### **B. Автоматически заполнять `day` и `month` при сохранении:**

```python
class Event(models.Model):
    title = models.CharField(max_length=100)
    date = models.DateField()
    day = models.IntegerField(editable=False)    # ← Скрыть из формы
    month = models.IntegerField(editable=False)  # ← Скрыть из формы
    
    def save(self, *args, **kwargs):
        """Автоматически заполняем day и month из date"""
        if self.date:
            self.day = self.date.day
            self.month = self.date.month
        super().save(*args, **kwargs)
```

**Теперь:**
```python
event = Event.objects.create(
    title="Открытие музея",
    date="2024-03-15"
)
print(event.day)    # 15 (автоматически)
print(event.month)  # 3 (автоматически)
```

---

#### **C. Создать миграцию:**

```bash
python manage.py makemigrations
```

Django спросит, что делать со старыми записями:

```
You are trying to add a non-nullable field 'day' to event without a default
Please select a fix:
 1) Provide a one-off default now
 2) Quit and manually add a default value to day
```

Выберите **1** и введите **1** (временное значение).

Затем создайте **data migration** для заполнения существующих записей:

```bash
python manage.py makemigrations --empty dates_and_persons
```

Отредактируйте созданный файл миграции:

```python
# dates_and_persons/migrations/0002_populate_day_month.py

from django.db import migrations

def populate_day_month(apps, schema_editor):
    Event = apps.get_model('dates_and_persons', 'Event')
    for event in Event.objects.all():
        event.day = event.date.day
        event.month = event.date.month
        event.save()

class Migration(migrations.Migration):
    dependencies = [
        ('dates_and_persons', '0001_add_day_month'),
    ]

    operations = [
        migrations.RunPython(populate_day_month),
    ]
```

Примените миграции:

```bash
python manage.py migrate
```

---

### ✅ **РЕШЕНИЕ 2: Использовать вычисляемые поля (без изменения БД)**

Если не хотите менять структуру БД, используйте `@property`:

```python
class Event(models.Model):
    title = models.CharField(max_length=100)
    date = models.DateField()
    
    @property
    def day(self):
        """Вычисляемое поле - день из даты"""
        return self.date.day
    
    @property
    def month(self):
        """Вычисляемое поле - месяц из даты"""
        return self.date.month
```

**Использование:**
```python
event = Event.objects.get(id=394)
print(event.day)    # 1 (вычисляется из date)
print(event.month)  # 1 (вычисляется из date)
```

**Проблема:** Нельзя фильтровать напрямую:
```python
# ❌ НЕ РАБОТАЕТ:
Event.objects.filter(day=15)  # ошибка!

# ✅ РАБОТАЕТ (но медленно):
Event.objects.filter(date__day=15)
```

---

### ✅ **РЕШЕНИЕ 3: Фильтрация без дополнительных полей**

Используйте встроенные lookup'ы Django:

```python
# Все события января (любого года)
Event.objects.filter(date__month=1)

# Все события 15 числа (любого месяца/года)
Event.objects.filter(date__day=15)

# Все события 15 марта (любого года)
Event.objects.filter(date__day=15, date__month=3)
```

---

### 📊 **СРАВНЕНИЕ РЕШЕНИЙ:**

| Решение | Плюсы | Минусы |
|---------|-------|--------|
| **Добавить `day` и `month` в БД** | ✅ Быстрые запросы<br>✅ Простая фильтрация | ❌ Дублирование данных<br>❌ Нужна миграция |
| **Использовать `@property`** | ✅ Нет дублирования<br>✅ Не нужна миграция | ❌ Нельзя фильтровать напрямую |
| **Использовать `date__day`** | ✅ Нет дублирования<br>✅ Не нужна миграция | ⚠️ Немного медленнее |

---

### 🎯 **РЕКОМЕНДАЦИЯ ДЛЯ ВАШЕГО ПРОЕКТА:**

**Используйте РЕШЕНИЕ 3** — встроенные lookup'ы `date__day` и `date__month`.

**Почему:**
- ✅ Не нужно менять БД
- ✅ Нет дублирования данных
- ✅ PostgreSQL хорошо оптимизирует такие запросы
- ✅ В вашем проекте всего 195 событий (небольшой объем)

**В API будете использовать:**
```python
class EventViewSet(viewsets.ModelViewSet):
    queryset = Event.objects.all()
    
    def get_queryset(self):
        queryset = super().get_queryset()
        
        # Фильтр по месяцу: /api/v1/events/?month=1
        month = self.request.query_params.get('month')
        if month:
            queryset = queryset.filter(date__month=month)
        
        # Фильтр по дню: /api/v1/events/?day=15
        day = self.request.query_params.get('day')
        if day:
            queryset = queryset.filter(date__day=day)
        
        return queryset
```

---

## 📝 **ИТОГОВЫЕ РЕКОМЕНДАЦИИ:**

### 1. **Промежуточные таблицы:**
- ✅ Удалите ручные промежуточные модели
- ✅ Используйте `ManyToManyField` в моделях Event и Person

### 2. **Названия полей:**
- ✅ Переименуйте `person_id` → `person`
- ✅ Переименуйте `event_id` → `event`
- ✅ Django автоматически добавит `_id` в БД

### 3. **Поля `day` и `month`:**
- ✅ НЕ добавляйте отдельные поля
- ✅ Используйте `date__day` и `date__month` в запросах
- ✅ Если нужно в API — добавьте через `SerializerMethodField`

Хотите, чтобы я создал файл с исправленными моделями? 😊




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














---

## 💻 Примеры для Next.js

### Использование новых полей

```typescript
// app/components/EventCard.tsx

interface Event {
  id: number
  title: string
  date: string
  day: number        // ← НОВОЕ!
  month: number      // ← НОВОЕ!
  image: string | null
  street: {          // ← НОВОЕ! (вложенный объект)
    id: number
    name: string
  } | null
}

export function EventCard({ event }: { event: Event }) {
  return (
    <div className="event-card">
      <h2>{event.title}</h2>
      
      {/* Использование готовых day/month */}
      <p>День: {event.day}, Месяц: {event.month}</p>
      
      {/* Использование вложенного объекта улицы */}
      {event.street && (
        <p>Место: {event.street.name}</p>
      )}
      
      <img src={event.image || '/placeholder.jpg'} alt={event.title} />
    </div>
  )
}
```

---

### Фильтрация по месяцам

```typescript
// app/calendar/page.tsx

'use client'

import { useState, useEffect } from 'react'
import { getEvents } from '@/lib/api'

export default function CalendarPage() {
  const [events, setEvents] = useState([])
  const [selectedMonth, setSelectedMonth] = useState(10)
  
  useEffect(() => {
    async function loadEvents() {
      const data = await getEvents()
      
      // Фильтруем по месяцу используя готовое поле month
      const filtered = data.results.filter(event => event.month === selectedMonth)
      
      setEvents(filtered)
    }
    
    loadEvents()
  }, [selectedMonth])
  
  return (
    <div>
      <h1>Календарь событий</h1>
      
      <select onChange={(e) => setSelectedMonth(Number(e.target.value))}>
        {[1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12].map(month => (
          <option key={month} value={month}>
            Месяц {month}
          </option>
        ))}
      </select>
      
      {events.map(event => (
        <EventCard key={event.id} event={event} />
      ))}
    </div>
  )
}
```

---

### Использование full_name и short_name

```typescript
// app/components/PersonCard.tsx

interface Person {
  id: number
  last_name: string
  first_name: string
  middle_name: string
  full_name: string      // ← НОВОЕ!
  short_name: string     // ← НОВОЕ!
  birth_date: string
  death_date: string | null
  image: string | null
}

export function PersonCard({ person }: { person: Person }) {
  return (
    <div className="person-card">
      {/* Используем готовое full_name вместо склеивания */}
      <h2>{person.full_name}</h2>
      
      {/* Используем готовое short_name для компактного отображения */}
      <p className="subtitle">{person.short_name}</p>
      
      <p>{person.birth_date} – {person.death_date || 'настоящее время'}</p>
      
      <img src={person.image || '/placeholder.jpg'} alt={person.full_name} />
    </div>
  )
}
```

---

### Работа с вложенными объектами

```typescript
// app/person/[id]/page.tsx

interface PersonDetail {
  id: number
  full_name: string
  short_name: string
  professions: Array<{ id: number; name: string }>     // ← НОВОЕ!
  streets: Array<{ id: number; name: string }>         // ← НОВОЕ!
  books: Array<{ id: number; author: string; title: string }>  // ← НОВОЕ!
  keywords: Array<{ id: number; keyword: string }>     // ← НОВОЕ!
  events: Array<Event>                                 // ← НОВОЕ!
}

export default async function PersonPage({ params }: { params: { id: string } }) {
  const person: PersonDetail = await getPerson(Number(params.id))
  
  return (
    <div>
      <h1>{person.full_name}</h1>
      
      {/* Профессии */}
      <section>
        <h2>Профессии</h2>
        {person.professions.map(prof => (
          <span key={prof.id} className="badge">{prof.name}</span>
        ))}
      </section>
      
      {/* Улицы */}
      <section>
        <h2>Улицы</h2>
        {person.streets.map(street => (
          <div key={street.id}>{street.name}</div>
        ))}
      </section>
      
      {/* События */}
      <section>
        <h2>Связанные события</h2>
        {person.events.map(event => (
          <EventCard key={event.id} event={event} />
        ))}
      </section>
      
      {/* Книги */}
      <section>
        <h2>Литература</h2>
        {person.books.map(book => (
          <div key={book.id}>
            {book.author} — {book.title}
          </div>
        ))}
      </section>
    </div>
  )
}
```

---

## 📊 Сводная таблица изменений

### Events API

| Поле | Было | Стало | Тип |
|------|------|-------|-----|
| `street` | ID (number) | Объект {id, name} | object/null |
| `day` | ❌ Не было | ✅ Добавлено | integer |
| `month` | ❌ Не было | ✅ Добавлено | integer |
| `persons` | ❌ Не было (только в Detail) | ✅ Добавлено | array |
| `keywords` | ❌ Не было (только в Detail) | ✅ Добавлено | array |
| `books` | ❌ Не было (только в Detail) | ✅ Добавлено | array |

### Persons API

| Поле | Было | Стало | Тип |
|------|------|-------|-----|
| `full_name` | ❌ Не было | ✅ Добавлено | string |
| `short_name` | ❌ Не было | ✅ Добавлено | string |
| `professions` | ❌ Не было (только в Detail) | ✅ Добавлено | array |
| `streets` | ❌ Не было (только в Detail) | ✅ Добавлено | array |
| `books` | ❌ Не было (только в Detail) | ✅ Добавлено | array |
| `keywords` | ❌ Не было (только в Detail) | ✅ Добавлено | array |
| `events` | ❌ Не было (только в Detail) | ✅ Добавлено | array |

---

## 🎯 Миграция с старого API

### ❌ Старый код (НЕ РАБОТАЕТ):

```typescript
// Склеивание ФИО вручную
const fullName = `${person.last_name} ${person.first_name} ${person.middle_name}`

// Парсинг даты для получения месяца
const date = new Date(event.date)
const month = date.getMonth() + 1

// Использование ID улицы
<p>Street ID: {event.street}</p>
```

### ✅ Новый код (ПРАВИЛЬНО):

```typescript
// Готовое ФИО
<h1>{person.full_name}</h1>
<p>{person.short_name}</p>

// Готовые день и месяц
<p>Месяц: {event.month}, День: {event.day}</p>

// Вложенный объект улицы
{event.street && <p>{event.street.name}</p>}
```

---

## ✅ Итого

**Версия API:** 2.0  
**Дата обновления:** 11 февраля 2026  
**Статус:** ✅ Полностью протестировано  

**Основные улучшения:**
1. ✅ Добавлены вычисляемые поля `day`, `month` для событий
2. ✅ Добавлены вычисляемые поля `full_name`, `short_name` для персон
3. ✅ Вложенные объекты вместо ID для улиц
4. ✅ Автоматическая загрузка связанных данных в Detail endpoints
5. ✅ Консистентная структура API между List и Detail

**Готово к продакшену!** 🚀
