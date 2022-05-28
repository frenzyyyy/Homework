1.

  - C:\post>docker run --name pdb -v back:/back -v main:/main -p 5432:5432 -e POSTGRES_PASSWORD=5566496 -d postgres
880203a0518cbe816dbaadb2d74ff9b918b4b2cae1b4efbdd7f73a67ab899bfd


2.
  - Create database test-db;

  - test-db=# create table orders (id serial primary key , name char(25) , price integer );

  - create table clients (id serial primary key , fio char(30) , counry char(25) , invoice integer , FOREIGN KEY (invoice) REFERENCES orders (
Id));
    
  - test-db=# create role "test-admin-user" superuser nocreatedb nocreaterole noinherit login;

CREATE ROLE
    
  - CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
test-db=# grant select on table public.clients to "test-simple-user";
    
GRANT

test-db=# grant insert on table public.clients to "test-simple-user";

GRANT

test-db=# grant update on table public.clients to "test-simple-user";

GRANT

test-db=# grant delete on table public.clients to "test-simple-user";

GRANT

test-db=# grant select on table public.orders to "test-simple-user";

GRANT

test-db=# grant insert on table public.orders to "test-simple-user";

GRANT

test-db=# grant update on table public.orders to "test-simple-user";

GRANT

test-db=# grant delete on table public.orders to "test-simple-user";

GRANT

  - test-db=# \dt
          List of relations
    
 Schema |  Name   | Type  |  Owner
    
--------+---------+-------+----------

 public | clients | table | postgres

 public | orders  | table | postgres

(2 rows)

  - test-db=# \du
                                       List of roles
    
    Role name     |                         Attributes                         | Member of

------------------+------------------------------------------------------------+-----------

 postgres         | Superuser, Create role, Create DB, Replication, Bypass RLS | {}

 test-admin-user  | Superuser, No inheritance                                  | {}

 test-simple-user | No inheritance                                             | {}


  - test-db=# \l
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
(5 rows)



3. 

  - test-db=# insert into orders VALUES (1, 'Chocolate', 10), (2, 'Printer', 3000), (3, 'Book', 500), (4, 'Monitor', 7000), (5, 'Guitar', 4000);

INSERT 0 5

test-db=# select count (*) from orders;
 count
_-
     5
(1 row)


  - test-db=# insert into clients VALUES (1, 'Ivanov Ivan Ivanovich', 'USA'), (2, 'Petrov Petr Petrovich', 'Canada'), (3, 'Iogann Sebastian Bakh', 'Japa
n'), (4, 'Ronny James Dio', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
    
INSERT 0 5

  - select count (*) from clients;

4.

  - update  clients set invoice = 3 where id = 1;

  - update  clients set invoice = 4 where id = 2;

  - update  clients set invoice = 5 where id = 3;

  - test-db=# select * from clients where invoice is not null;

 id |              fio               |          counry           | invoice

----+--------------------------------+---------------------------+---------

  1 | Ivanov Ivan Ivanovich          | USA                       |       3

  2 | Petrov Petr Petrovich          | Canada                    |       4

  3 | Iogann Sebastian Bakh          | Japan                     |       5

(3 rows)

5.

  - test-db=# explain select * from clients where invoice is not null;
    
                         QUERY PLAN

------------------------------------------------------------

 Seq Scan on clients  (cost=0.00..13.00 rows=298 width=236)

   Filter: (invoice IS NOT NULL)

(2 rows)

  - Показывает нагрузку на исполнение запроса, тип фильтра invoice IS NOT NULL

6.

  - docker exec -t pdb pg_dumpall -c -U postgres > back\dump.sql

- docker exec -i pdb2 psql -U postgres -d test-db -f back\dump.sql
   


