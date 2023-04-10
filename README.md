### Api_YaMDb
Проект YaMDb собирает отзывы пользователей на произведения.
Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.
### Благодаря этому проекту пользователи смогут:
 - Зарегистрироваться
 - Оставить отзыв и оценку на произведение
 - Оставить комментарий к отзыву 
 - Многое другое

Проект упакован в Docker контейнеры.

Образ на DockerHub: niklukyan/api_yamdb:v2.0

### Для подготовки .env файла необходимо заполнить все нужные переменные в файле шаблоне .env.template и выполнить команду:
```
cp infra/.env.template infra/.env 
```

### Запуск приложения в контейнерах
Клонировать репозиторий и перейти в папку infra в командной строке:
```
git clone https://github.com/niklukyan/infra_sp2.git
```
Запустить сборку контейнеров docker-compose:
```
sudo docker compose up -d
```
Провести миграции:
```
sudo docker compose exec web python manage.py migrate
```

Создать суперпользователя:
```
sudo docker compose exec web python manage.py createsuperuser
```
Собрать статику:
```
sudo docker compose exec web python manage.py collectstatic --no-input
```
### Команды для заполнения базы данных
### Вариант 1
Копировать заранее сохраненные фикстуры из папки data в папку infra c помощью команды:
```
cp data/fixtures.json infra/fixtures.json
```
### Вариант 2
Создать дамп (резервную копию) базы данных "fixtures.json" можно следующей командой:
```
sudo docker-compose exec web python manage.py dumpdata > fixtures.json
```
Далее команды по востановлению базы данных из резервной копии. Узнаем CONTAINER ID для контейнера с джанго - "infra-web-1":
```
sudo docker container ls -a
```
Копируем файл "fixtures.json" с фикстурами в контейнер::
```
sudo docker cp fixtures.json <CONTAINER ID>:/app
```
Применяем фикстуры:
```
sudo docker-compose exec web python manage.py loaddata fixtures.json
```
Удаляем файл "fixtures.json" из контейнера:
```
sudo docker exec -it <CONTAINER ID> bash
rm fixtures.json
exit
```
### Документация
- Когда вы запустите проект, по нижеуказанному адресу будет 
доступна полная документация для API YaMDb в формате Swager 
```
/api/schema/swagger-ui/ 
```
и Redoc 
```
/api/schema/redoc/
```

### Примеры 
### Самостоятельная регистрация новых пользователей
- Пользователь отправляет POST-запрос с параметрами email и username на эндпоинт 
```
/api/v1/auth/signup/
``` 
- Сервис YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на указанный адрес email.
- Пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт 
```
/api/v1/auth/token/
```
- В ответе на запрос ему приходит token (JWT-токен).

- В результате пользователь получает токен и может работать с API проекта, отправляя этот токен с каждым запросом. 
После регистрации и получения токена пользователь может отправить PATCH-запрос на эндпоинт
```
/api/v1/users/me/
```
и заполнить поля в своём профайле (описание полей — в документации).

### Авторы
 
```
Никита Лукьянчук (8-918-261-01-04 nikluk@mail.ru), Елена Тимофеева, Дмитрий Бессмертный
``` 