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
```
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 postgres | postgres         | test_db       | public       | orders     | INSERT         | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | SELECT         | YES          | YES
 postgres | postgres         | test_db       | public       | orders     | UPDATE         | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | DELETE         | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | TRUNCATE       | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | REFERENCES     | YES          | NO
 postgres | postgres         | test_db       | public       | orders     | TRIGGER        | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | INSERT         | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | SELECT         | YES          | YES
 postgres | postgres         | test_db       | public       | clients    | UPDATE         | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | DELETE         | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | TRUNCATE       | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | REFERENCES     | YES          | NO
 postgres | postgres         | test_db       | public       | clients    | TRIGGER        | YES          | NO
 postgres | test-admin-user  | test_db       | public       | orders     | INSERT         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | SELECT         | NO           | YES
 postgres | test-admin-user  | test_db       | public       | orders     | UPDATE         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | DELETE         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | TRUNCATE       | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | REFERENCES     | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | TRIGGER        | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | INSERT         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | SELECT         | NO           | YES
 postgres | test-admin-user  | test_db       | public       | clients    | UPDATE         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | DELETE         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | TRUNCATE       | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | REFERENCES     | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | TRIGGER        | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES
 postgres | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES
 postgres | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO
(36 rows)

```
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
```sql

SELECT COUNT(*) FROM ORDERS;	-- Количество записей в таблице ORDERS

count
-------
     5
(1 row)

SELECT COUNT(*) FROM CLIENTS;	-- Количество записей в таблице CLIENTS

count
-------
     5
(1 row) 
 ``` 

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.
```sql
UPDATE clients SET заказ = 3 WHERE clients.id = 1;
UPDATE clients SET заказ = 4 WHERE clients.id = 2;
UPDATE clients SET заказ = 5 WHERE clients.id = 3;
```
Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
```sql
SELECT c.фамилия, o.Наименование, o.Цена
FROM clients c INNER JOIN ORDERS o	on c.заказ = o.id
```
```
"Иванов Иван Иванович"	"Книга"	500
"Петров Петр Петрович"	"Монитор"	7000
"Иоганн Себастьян Бах"	"Гитара"	4000
```
Подсказк - используйте директиву `UPDATE`.

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

```
"Hash Join  (cost=17.20..29.36 rows=170 width=440)"
"  Hash Cond: (c."заказ" = o.id)"
"  ->  Seq Scan on clients c  (cost=0.00..11.70 rows=170 width=222)"
"  ->  Hash  (cost=13.20..13.20 rows=320 width=226)"
"        ->  Seq Scan on orders o  (cost=0.00..13.20 rows=320 width=226)"
Данный результат отображает:
 - Приблизительная стоимость запуска. Это время, которое проходит, прежде чем начнётся этап вывода данных.
 - Приблизительная общая стоимость. Она вычисляется в предположении, что узел плана выполняется до конца, то есть возвращает все доступные строки.
 - Ожидаемое число строк, которое должен вывести этот узел плана. При этом так же предполагается, что узел выполняется до конца.
 - Ожидаемый средний размер строк, выводимых этим узлом плана (в байтах).
```

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).
```bash
# Для выполнения использую утилиту pg_dump, которую запускаю внутри контейнера docker:
root@vagrant:~# docker exec -it 632989652e9d pg_dump test_db -h localhost -U postgres -f /var/lib/postgresql/backup/dump.sql
```
Остановите контейнер с PostgreSQL (но не удаляйте volumes).
```bash
root@vagrant:~# docker ps
CONTAINER ID   IMAGE                 COMMAND                  CREATED        STATUS        PORTS                                        NAMES
6f8c81fd60eb   dpage/pgadmin4:4.18   "/entrypoint.sh"         22 hours ago   Up 22 hours   0.0.0.0:80->80/tcp, :::80->80/tcp, 443/tcp   postgres_pgadmin_1
632989652e9d   postgres:12           "docker-entrypoint.s…"   22 hours ago   Up 22 hours   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp    pgsql-server
root@vagrant:~# docker stop 632989652e9d
632989652e9d
```
Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

