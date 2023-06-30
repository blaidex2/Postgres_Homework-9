## Домашнее задание №9

1.	Создаем ВМ/докер c ПГ.

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/da32d2e9-4ea6-4540-8937-d69b31ccb01e)

>sudo apt update && sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y -q && sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list' && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add - && sudo apt-get update && sudo DEBIAN_FRONTEND=noninteractive apt -y install postgresql-15

2.	Создаем БД, схему и в ней таблицу.
   
>sudo -u postgres psql
create database testdb;
\c testdb
create table test1 (id serial, str varchar(100));

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/7ee9830d-886e-405c-b22b-7c49ad58cd3e)


3.	Заполним таблицы автосгенерированными 100 записями.
   
>insert into test1 (id, str) select generate_series(1,100), md5(random()::text)::char(10);
select count(id) from test1;

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/767befd6-ecb6-4da4-a565-15145478e8a6)


4.	Под линукс пользователем Postgres создадим каталог для бэкапов

>sudo -i
su postgres
mkdir /var/lib/postgresql/15/main/testbackup

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/70d042d3-dc0f-46ff-a774-983fea14efd5)

5.	Сделаем логический бэкап используя утилиту COPY
   
>sudo -u postgres psql -d testdb
\copy test1 to '/var/lib/postgresql/15/main/testbackup/b1.csv' with delimiter '|';

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/5aa4b1c1-2ade-4278-aa12-561c405f0acf)

 
6.	Восстановим во вторую таблицу данные из бэкапа.

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/50485fac-0fb4-496e-9e9e-18d91db1ebff)


7.	Используя утилиту pg_dump создадим бэкап в кастомном сжатом формате двух таблиц
    
>alter user postgres password 'qwerty';

>pg_dump -h localhost -d testdb -t test1 -t test2 -Fc -C -U postgres > db.bak

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/fd09de29-c2d5-4418-a4fe-4d7d783c16b6)


8.	Используя утилиту pg_restore восстановим в новую БД только вторую таблицу!
   
>create database testdb_restore;
pg_restore -h localhost -d testdb_restore -U postgres -t test2 db.bak
sudo -u postgres psql -d testdb_restore

![image](https://github.com/blaidex2/Postgres_Homework-9/assets/130083589/2266ffb8-8668-4841-97f7-d2b5b6402525)

