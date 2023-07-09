# Домашнее задание к занятию "`MySQL`" - `Живарев Игорь`


### Задание 1

Используя Docker, поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите бэкап БД и восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h`, получите список управляющих команд.

Найдите команду для выдачи статуса БД и приведите в ответе из её вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

Приведите в ответе количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с этим контейнером.


Ответ:

```
╭─shaman@Barton ~/docker/mysql 
╰─$ sudo docker-compose up -d        
[+] Running 12/12
 ✔ db 11 layers [⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿⣿]      0B/0B      Pulled                                                                                                         90.8s 
   ✔ e2c03c89dcad Pull complete                                                                                                                              56.2s 
   ✔ 68eb43837bf8 Pull complete                                                                                                                              56.5s 
   ✔ 796892ddf5ac Pull complete                                                                                                                              56.8s 
   ✔ 6bca45eb31e1 Pull complete                                                                                                                              57.5s 
   ✔ ebb53bc0dcca Pull complete                                                                                                                              57.8s 
   ✔ 2e2c6bdc7a40 Pull complete                                                                                                                              58.2s 
   ✔ 6f27b5c76970 Pull complete                                                                                                                              73.7s 
   ✔ 438533a24810 Pull complete                                                                                                                              74.0s 
   ✔ e5bdf19985e0 Pull complete                                                                                                                              85.3s 
   ✔ 667fa148337b Pull complete                                                                                                                              86.1s 
   ✔ 5baa702110e4 Pull complete                                                                                                                              86.4s 
[+] Running 1/1
 ✔ Container mysql  Started                                                                                                                                   4.5s 
╭─shaman@Barton ~/docker/mysql 
╰─$ sudo docker exec -it mysql bash  
[sudo] пароль для shaman: 
bash-4.4# mysql -u msql -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.33 MySQL Community Server - GPL

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

Содержание docker-compose.yaml:

```
version: '3.1'

services:
  db:
    image: mysql
    container_name: mysql
    environment:
      MYSQL_USER: msql
      MYSQL_PASSWORD: msql
      MYSQL_DATABASE: test_db
      MYSQL_ROOT_PASSWORD: test123
    ports:
      - 3306:3306
    volumes:
#    - $HOME/docker/mysql/:/opt/backup/
#    - $HOME/docker/mysql/my-db/:/var/lib/mysql
      - my-backup:/opt/backup/
#     - my-db:/var/lib/mysql
#    restart: always

volumes:
  my-backup:
#  my-db:

```

Копирование бэкапа в контейнер и востановление данных из него:

```
╭─shaman@Barton ~/docker/mysql 
╰─$ sudo docker cp test_dump.sql mysql:/opt/backup/  
Successfully copied 4.1kB to mysql:/opt/backup/
╭─shaman@Barton ~/docker/mysql 
╰─$ sudo docker exec -it mysql bash                
bash-4.4# mysql -u root -p test_db < /opt/backup/test_dump.sql
Enter password: 
bash-4.4#

```

Выполнение команды \h

```
mysql> \h

For information about MySQL products and services, visit:
   http://www.mysql.com/
For developer information, including the MySQL Reference Manual, visit:
   http://dev.mysql.com/
To buy MySQL Enterprise support, training, or other products, visit:
   https://shop.mysql.com/

List of all MySQL commands:
Note that all text commands must be first on line and end with ';'
?         (\?) Synonym for `help'.
clear     (\c) Clear the current input statement.
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.
resetconnection(\x) Clean session context.
query_attributes Sets string parameters (name1 value1 name2 value2 ...) for the next query to pick up.
ssl_session_data_print Serializes the current SSL session data to stdout or file

For server side help, type 'help contents'

mysql> 
```

Вывод статуса:

```
mysql> \s
--------------
mysql  Ver 8.0.33 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:		12
Current database:	
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		8.0.33 MySQL Community Server - GPL
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	utf8mb4
Db     characterset:	utf8mb4
Client characterset:	latin1
Conn.  characterset:	latin1
UNIX socket:		/var/run/mysqld/mysqld.sock
Binary data as:		Hexadecimal
Uptime:			42 min 21 sec

Threads: 2  Questions: 42  Slow queries: 0  Opens: 139  Flush tables: 3  Open tables: 57  Queries per second avg: 0.016
--------------

mysql> 
```

Переключение в востановленную БД и вывод количества записей с `price` > 300:

```
mysql> USE test_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SELECT COUNT(*) FROM orders WHERE price > 300;
+----------+
| COUNT(*) |
+----------+
|        1 |
+----------+
1 row in set (0.01 sec)

mysql> 
```

---

### Задание 2

Создайте пользователя `test` в БД c паролем test-pass, используя:

- плагин авторизации mysql_native_password
- срок истечения пароля — 180 дней
- количество попыток авторизации — 3
- максимальное количество запросов в час — 100
- аттрибуты пользователя:
  - Фамилия "Pretty"
  - Имя "James".
Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.

Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES, получите данные по пользователю `test` и приведите в ответе к задаче.


Ответ:

Создание пользователя test:

```
mysql> CREATE USER 'test'@'localhost' 
    ->     IDENTIFIED WITH mysql_native_password BY 'test-pass'
    ->     WITH MAX_CONNECTIONS_PER_HOUR 100
    ->     PASSWORD EXPIRE INTERVAL 180 DAY
    ->     FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2
    ->     ATTRIBUTE '{"first_name":"James", "last_name":"Pretty"}';
Query OK, 0 rows affected (0.17 sec)

mysql> 
```

Предоставление привелегий пользователю test:

```
mysql> GRANT SELECT ON test_db.* TO test@localhost;
Query OK, 0 rows affected, 1 warning (0.10 sec)

mysql> 
```

Получение данных по пользователю test:

```
mysql> SELECT * from INFORMATION_SCHEMA.USER_ATTRIBUTES where USER = 'test';
+------+-----------+------------------------------------------------+
| USER | HOST      | ATTRIBUTE                                      |
+------+-----------+------------------------------------------------+
| test | localhost | {"last_name": "Pretty", "first_name": "James"} |
+------+-----------+------------------------------------------------+
1 row in set (0.00 sec)

mysql>
```

---

### Задание 3

Установите профилирование `SET profiling = 1`. Изучите вывод профилирования команд `SHOW PROFILES`;.

Исследуйте, какой `engine` используется в таблице БД `test_db` и приведите в ответе.

Измените `engine` и приведите время выполнения и запрос на изменения из профайлера в ответе:

- на `MyISAM`,
- на `InnoDB`.


Ответ:

Установка профилирования и вывод команды `SHOW PROFILES`:

```
mysql> SET profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> SHOW PROFILES;
Empty set, 1 warning (0.00 sec)

mysql>
```

Какой `engine` используется в таблице БД `test_db`:

```
mysql> SELECT table_schema,table_name,engine FROM information_schema.tables WHERE table_schema = DATABASE();
+--------------+------------+--------+
| TABLE_SCHEMA | TABLE_NAME | ENGINE |
+--------------+------------+--------+
| test_db      | orders     | InnoDB |
+--------------+------------+--------+
1 row in set (0.01 sec)

mysql>
```

Время выполнения и запрос на изменения из профайлера:

```
mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (1.36 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE orders ENGINE = InnoDB;
Query OK, 5 rows affected (0.89 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SHOW PROFILES;
+----------+------------+------------------------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                                |
+----------+------------+------------------------------------------------------------------------------------------------------+
|        1 | 0.00186825 | SELECT table_schema,table_name,engine FROM information_schema.tables WHERE table_schema = DATABASE() |
|        2 | 1.35973075 | ALTER TABLE orders ENGINE = MyISAM                                                                   |
|        3 | 0.89334850 | ALTER TABLE orders ENGINE = InnoDB                                                                   |
+----------+------------+------------------------------------------------------------------------------------------------------+
3 rows in set, 1 warning (0.00 sec)

mysql>
```

### Задание 4

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):

- скорость IO важнее сохранности данных;
- нужна компрессия таблиц для экономии места на диске;
- размер буффера с незакомиченными транзакциями 1 Мб;
- буффер кеширования 30% от ОЗУ;
- размер файла логов операций 100 Мб.
Приведите в ответе изменённый файл `my.cnf`.


Ответ:

Файл my.cnf в директории /etc:

```
bash-4.4# cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
bash-4.4#
```

Изменение файла my.cnf согласно заданию:

```
bash-4.4# cat /etc/my.cnf
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
secure-file-priv= NULL

# Custom config should go here
!includedir /etc/mysql/conf.d/
innodb_flush_log_at_trx_commit = 0
innodb_file_format=Barracuda
innodb_log_buffer_size= 1M
key_buffer_size = 300M
max_binlog_size= 100M
bash-4.4#
```
