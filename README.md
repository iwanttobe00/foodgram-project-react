![example workflow](https://github.com/iwanttobe00/foodgram-project-react/actions/workflows/main.yml/badge.svg)
### Опиание проекта.
Сайт Foodgram, «Продуктовый помощник». Это онлайн-сервис и API для него. На этом сервисе пользователи могут публиковать рецепты, подписываться на публикации других пользователей, добавлять понравившиеся рецепты в список «Избранное», а перед походом в магазин скачивать сводный список продуктов, необходимых для приготовления одного или нескольких выбранных блюд.

Foodgram - проект позволяет:

- Просматривать рецепты
- Добавлять рецепты в избранное
- Добавлять рецепты в список покупок
- Создавать, удалять и редактировать собственные рецепты
- Скачивать список покупок

Сервис будет доступен:  
http://51.250.0.153
При локальном развертывании - http://localhost/api/v1  
Документация - http://localhost/redoc  

При развертывании на сервере - http://<ip_адрес_хоста>/api/v1  
Документация - http://<ip_адрес_хоста>/redoc

## Инструкции по установке локально
***- Установите и активируйте виртуальное окружение:***
- для MacOS
```
python3 -m venv venv
```
- для Windows
```
python -m venv venv
source venv/bin/activate
source venv/Scripts/activate
```

***- Установите зависимости из файла requirements.txt:***
```
pip install -r requirements.txt
```

***- Примените миграции:***
```
python manage.py migrate
```

***- В папке с файлом manage.py выполните команду для запуска локально:***
```
python manage.py runserver
```

### Инструкции для развертывания и запуска приложения
- Зайдите на удаленный сервер
    ```bash
    ssh username@<host>
    ```
- Установите docker 
  ```bash
  sudo apt install docker.io
  ```

- Установите docker-compose на сервер:
  ```bash
  curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  chmod +x /usr/local/bin/docker-compose
  ```

- Скопируйте файлы docker-compose.yml и nginx.conf из директории infra на сервер:
  ```bash
  scp docker-compose.yml <username>@<host>:/home/<username>/
  scp nginx.conf <username>@<host>:/home/<username>/
  ```

- Для работы с Workflow добавьте в Secrets GitHub переменные окружения для работы:
```bash
  - DOCKER_USERNAME=<логин от аккаунта на Docker Hub>
  - DOCKER_PASSWORD=<пароль от аккаунта на Docker Hub>

  - HOST=<публичный адрес сервера для доступа по SSH>
  - USER=<username для подключения к серверу> 
  - SSH_KEY=<ваш SSH ключ (для получения команда: cat ~/.ssh/id_rsa)>
  - PASSPHRASE=<пароль для сервера, если он установлен>

  - DB_ENGINE=<django.db.backends.postgresql>
  - DB_NAME=<имя базы данных postgres>
  - DB_POSTGRES_USER=<пользователь бд>
  - DB_POSTGRES_PASSWORD=<пароль>
  - DB_HOST=<db>
  - DB_PORT=<5432>

  - TELEGRAM_TOKEN=<токен вашего бота>. Получить этот токен можно у бота @BotFather
  - TELEGRAM_TO=<ID чата, в который придет сообщение>. Узнать свой ID можно у бота @userinfobot
```

- Так же, создаем файл .env на ВМ:
    ```bash
    touch .env
    ```
    А так же заполнить его данными:
    ```bash
    DB_ENGINE='django.db.backends.postgresql'
    POSTGRES_DB='name' # Задаем имя для БД.
    POSTGRES_USER='user' # Задаем пользователя для БД.
    POSTGRES_PASSWORD='pass' # Задаем пароль для БД.
    DB_HOST='db'
    DB_PORT='5432'
    TOKEN='secret'  # Задаем secret_key.
    ```
    
Соберите и запустите контейнеры на сервере:
  ```bash
  docker-compose up -d --build
  ```
- После успешной сборки выполните следующие действия (только при первом деплое):
    * проведите миграции внутри контейнеров:
      ```bash
      docker-compose exec web python manage.py migrate
      ```
    * соберите статику проекта:
      ```bash
      docker-compose exec web python manage.py collectstatic --no-input
      ```  
    * Создайте суперпользователя Django, после запроса от терминала введите логин и пароль для суперпользователя:
      ```bash
      docker-compose exec web python manage.py createsuperuser
      ```