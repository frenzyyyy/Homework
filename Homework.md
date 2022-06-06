1.  Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.
Приведите получившуюся команду или docker-compose манифест.

       C:\post>docker run --name pdb -v back:/back -v main:/main -p 5432:5432 -e POSTGRES_PASSWORD=5566496 -d postgres

_____


2. 
В БД из задачи 1:

  - создайте пользователя test-admin-user и БД test_db
  - в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
  - предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
  - создайте пользователя test-simple-user
  - предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db
  

  Приведите:

  - итоговый список БД после выполнения пунктов выше,
  - описание таблиц (describe)
  - SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
  - список пользователей с правами над таблицами test_db
  
 
        Create database test-db;

        test-db=# create table orders (id serial primary key , name char(25) , price integer );

        test-db=# create table clients (id serial primary key , fio char(30) , counry char(25) , invoice integer , FOREIGN KEY (invoice) REFERENCES orders (Id));
    
        test-db=# create role "test-admin-user" superuser nocreatedb nocreaterole noinherit login;

        CREATE ROLE
    
        test-db=# CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
        test-db=# grant select on table public.clients to "test-simple-user";
    
        GRANT

        test-db=# grant insert on table public.clients to "test-simple-user";

        test-db=# grant update on table public.clients to "test-simple-user";

        test-db=# grant delete on table public.clients to "test-simple-user";

        test-db=# grant select on table public.orders to "test-simple-user";

        test-db=# grant insert on table public.orders to "test-simple-user";

        test-db=# grant update on table public.orders to "test-simple-user";

        test-db=# grant delete on table public.orders to "test-simple-user";


        test-db=# \dt
          List of relations
    
        Schema |  Name   | Type  |  Owner
    
        --------+---------+-------+----------

        public | clients | table | postgres

        public | orders  | table | postgres


         test-db=# \du
                                       List of roles
    
    Role name     |                         Attributes                         | Member of

        ------------------+------------------------------------------------------------+-----------

        postgres         | Superuser, Create role, Create DB, Replication, Bypass RLS | {}

        test-admin-user  | Superuser, No inheritance                                  | {}

        test-simple-user | No inheritance                                             | {}


         test-db=# \l
                                 List of databases
         Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
        -----------+----------+----------+------------+------------+-----------------------
         postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
        template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
                  |          |          |            |            | postgres=CTc/postgres
        template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
                 |          |          |            |            | postgres=CTc/postgres
     test      | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
     test-db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |




3. 
Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:
Используя SQL синтаксис:

  - вычислите количество записей для каждой таблицы
  - приведите в ответе:
  - запросы
  - результаты их выполнения.
  

  - Наполнение:

         test-db=# insert into orders VALUES (1, 'Chocolate', 10), (2, 'Printer', 3000), (3, 'Book', 500), (4, 'Monitor', 7000), (5, 'Guitar', 4000);

        INSERT 0 5
  - Записи для таблицы orders:
  - 
        test-db=# select count (*) from orders;
        count
        _-
        5
        (1 row)


  - Наполнение таблицы clients:
  
         test-db=# insert into clients VALUES (1, 'Ivanov Ivan Ivanovich', 'USA'), (2, 'Petrov Petr Petrovich', 'Canada'), (3, 'Iogann Sebastian Bakh', 'Japan'), (4, 'Ronny James Dio', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
    
        INSERT 0 5

         test-db=# select count (*) from clients;

4. 
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.

Подсказк - используйте директиву UPDATE.

     update  clients set invoice = 3 where id = 1;

     update  clients set invoice = 4 where id = 2;

     update  clients set invoice = 5 where id = 3;

     test-db=# select * from clients where invoice is not null;

    id |              fio               |          counry           | invoice

    ----+--------------------------------+---------------------------+---------

     1 | Ivanov Ivan Ivanovich          | USA                       |       3

     2 | Petrov Petr Petrovich          | Canada                    |       4

     3 | Iogann Sebastian Bakh          | Japan                     |       5

    (3 rows)

5.
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

     test-db=# explain select * from clients where invoice is not null;
    
     QUERY PLAN



    Seq Scan on clients  (cost=0.00..13.00 rows=298 width=236)

    Filter: (invoice IS NOT NULL)

    (2 rows)
    - Цифры слева направо показывают:
        - Приблизительная стоимость запуска. Это время, которое проходит, прежде чем начнётся этап вывода данных.

        - Приблизительная общая стоимость. Она вычисляется в предположении, что узел плана выполняется до конца, то есть возвращает все доступные строки. 

        - Ожидаемое число строк, которое должен вывести этот узел плана. При этом так же предполагается, что узел выполняется до конца.

        - Ожидаемый средний размер строк, выводимых этим узлом плана (в байтах).
6. 
Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления.

 Бэкап:
 
         docker exec -t pdb pg_dumpall -c -U postgres > back\dump.sql

 Восстановление:
 
         docker exec -i pdb2 psql -U postgres -d test-db -f back\dump.sql
   


