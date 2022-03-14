## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

#### Ответ
Для удобства выполнения следующих задач также также установил в контейне pgAdmin
```yaml
version: '3.7'
services:
  db:
    image: postgres:12
    container_name: pgsql-server
    restart: always
    ports:
      - 5432:5432
    environment:
      POSTGRES_USER: 'postgres'
      POSTGRES_PASSWORD: 'postgres'
    volumes:
      - database_data:/var/lib/postgresql/data
      - database_backup:/var/lib/postgresql/backup
  pgadmin:
    image: dpage/pgadmin4:4.18
    restart: always
    environment:
      PGADMIN_DEFAULT_EMAIL: i@i.i
      PGADMIN_DEFAULT_PASSWORD: qaz
      PGADMIN_LISTEN_PORT: 80
    ports:
      - 80:80
    volumes:
      - pgadmin-data:/var/lib/pgadmin
    links:
      - 'db:pgsql-server'
volumes:
  database_data:
  pgadmin-data:
  database_backup:
```

## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
- создайте пользователя test-simple-user  
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,
```
test_db=# \l
                                     List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |       Access privileges
-----------+----------+----------+------------+------------+--------------------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres                   +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres                   +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =Tc/postgres                  +
           |          |          |            |            | postgres=CTc/postgres         +
           |          |          |            |            | "test-admin-user"=CTc/postgres
(4 rows)
```
- описание таблиц (describe)
```
test_db=# \d orders;
                                       Table "public.orders"
    Column    |          Type          | Collation | Nullable |              Default
--------------+------------------------+-----------+----------+------------------------------------
 id           | integer                |           | not null | nextval('orders_id_seq'::regclass)
 Наименование | character varying(100) |           |          |
 Цена         | integer                |           |          |
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "fk_order" FOREIGN KEY ("заказ") REFERENCES orders(id)

test_db=# \d clients;
                                         Table "public.clients"
      Column       |          Type          | Collation | Nullable |               Default
-------------------+------------------------+-----------+----------+-------------------------------------
 id                | integer                |           | not null | nextval('clients_id_seq'::regclass)
 фамилия           | character varying(100) |           |          |
 страна проживания | character varying(100) |           |          |
 заказ             | integer                |           |          |
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
    "clients_lower_idx" btree (lower("страна проживания"::text))
Foreign-key constraints:
    "fk_order" FOREIGN KEY ("заказ") REFERENCES orders(id)
```
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
```sql
SELECT *
FROM information_schema.table_privileges
WHERE table_name in (SELECT tablename
						FROM pg_catalog.pg_tables
						WHERE schemaname != 'pg_catalog' AND 
    					schemaname != 'information_schema')
```
- список пользователей с правами над таблицами test_db

## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
- приведите в ответе:
    - запросы 
    - результаты их выполнения.

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказк - используйте директиву `UPDATE`.

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

