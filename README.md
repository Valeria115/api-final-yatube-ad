# API для Yatube

## Описание

**Yatube API** — это REST API для социальной сети Yatube, где пользователи могут публиковать записи, комментировать их, а также подписываться на других авторов. Проект реализует полноценный бэкенд без фронтенда: все взаимодействие с данными происходит через API-запросы.

### Возможности:
- Просмотр, создание, редактирование и удаление публикаций
- Комментирование публикаций
- Просмотр сообществ (групп)
- Подписка/отписка на авторов
- Аутентификация через JWT-токены

## Установка

### 1. Клонировать репозиторий
```bash
git clone https://github.com/Valeria115/api-final-yatube-ad.git
cd api-final-yatube-ad
```

### 2. Создать и активировать виртуальное окружение
```bash
python -m venv venv

# Windows
venv\Scripts\activate

# Linux / macOS
source venv/bin/activate
```

### 3. Установить зависимости
```bash
pip install -r requirements.txt
```

### 4. Выполнить миграции
```bash
python manage.py migrate
```

### 5. Создать суперпользователя (опционально)
```bash
python manage.py createsuperuser
```

### 6. Запустить сервер
```bash
python manage.py runserver
```

После запуска API будет доступен по адресу `http://127.0.0.1:8000/api/v1/`, документация — по адресу `http://127.0.0.1:8000/redoc/`.

---

## Аутентификация

API использует JWT-токены. Чтобы получить токен:

```http
POST /api/v1/jwt/create/
Content-Type: application/json

{
    "username": "your_username",
    "password": "your_password"
}
```

Используйте полученный `access`-токен в заголовке каждого запроса:
```
Authorization: Bearer <ваш_токен>
```

---

## Примеры запросов

### Получить список публикаций (с пагинацией)
```http
GET /api/v1/posts/?limit=10&offset=0
```

**Ответ:**
```json
{
    "count": 3,
    "next": null,
    "previous": null,
    "results": [
        {
            "id": 1,
            "author": "username",
            "text": "Текст публикации",
            "pub_date": "2024-01-01T12:00:00Z",
            "image": null,
            "group": null
        }
    ]
}
```

---

### Создать публикацию
```http
POST /api/v1/posts/
Authorization: Bearer <токен>
Content-Type: application/json

{
    "text": "Текст новой публикации",
    "group": 1
}
```

**Ответ (201):**
```json
{
    "id": 2,
    "author": "username",
    "text": "Текст новой публикации",
    "pub_date": "2024-01-02T10:00:00Z",
    "image": null,
    "group": 1
}
```

---

### Добавить комментарий к публикации
```http
POST /api/v1/posts/1/comments/
Authorization: Bearer <токен>
Content-Type: application/json

{
    "text": "Отличная публикация!"
}
```

**Ответ (201):**
```json
{
    "id": 1,
    "author": "username",
    "text": "Отличная публикация!",
    "created": "2024-01-02T11:00:00Z",
    "post": 1
}
```

---

### Подписаться на пользователя
```http
POST /api/v1/follow/
Authorization: Bearer <токен>
Content-Type: application/json

{
    "following": "target_username"
}
```

**Ответ (201):**
```json
{
    "user": "my_username",
    "following": "target_username"
}
```

---

### Получить список своих подписок
```http
GET /api/v1/follow/
Authorization: Bearer <токен>
```

Можно фильтровать по имени пользователя:
```http
GET /api/v1/follow/?search=target
```
