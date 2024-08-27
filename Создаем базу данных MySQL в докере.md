1. Забрада старотовый репозиторий:
```sql
git clone https://github.com/aeuge/otus-mysql-docker.git
cd otus-mysql-docker
```
2. Прописала init.sql :
```sql

CREATE DATABASE test2;

USE test2;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE
);

INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');
INSERT INTO users (name, email) VALUES ('Bob', 'bob@example.com');
```
3. Проверила файл `docker-compose.yml`
```sql
version: '3.1'

services:
  mysql:
    image: mysql:8.0.15
    container_name: mysql_custom
    environment:
      MYSQL_ROOT_PASSWORD: 12345
      MYSQL_DATABASE: test2
      MYSQL_USER: testuser
      MYSQL_PASSWORD: testpassword
    command: --init-file=/init.sql
    volumes:
      - data:/var/lib/mysql
      - ./init.sql:/init.sql
      - ./custom.conf:/etc/mysql/mysql.cnf
    ports:
      - "3309:3306"

  sysbench:
    image: severalnines/sysbench
    container_name: sysbench
    depends_on:
      - mysql
    networks:
      - default
    entrypoint: /bin/bash
    command: -c "sleep 10; sysbench --db-driver=mysql --mysql-host=mysql --mysql-user=root --mysql-password=12345 --mysql-db=test2 --tables=1 --table-size=10000 --threads=4 --time=60 oltp_read_write prepar

volumes:
  data:
```
4. Подняла БД
```
docker-compose up otusdb
```
Проверила процесс через команду:
```sql
sudo docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS        PORTS                                                  NAMES
4df2a4e8255c   mysql:8.0.15   "docker-entrypoint.s…"   1s ago         Up  1s    33060/tcp, 0.0.0.0:3309->3306/tcp, :::3309->3306/tcp   otus-mysql-docker_otusdb_1
```
5. Подключилась:
```
#~/otus-mysql-docker$ sudo docker exec -it  4df2a4e8255c /bin/bash
root@4df2a4e8255c:/# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 22
Server version: 8.0.15 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

### Дополнительное задание:

Результаты теста производительности:
```bash
root@4df2a4e8255c:/sysbench-1.0.20# sysbench --db-driver=mysql --mysql-host=localhost --mysql-user=root --mysql-password=12345 --mysql-db=test2 --tables=1 --table-size=10000 --threads=4 --time=60 oltp_read_write run
sysbench 1.0.20 (using bundled LuaJIT 2.1.0-beta2)

Running the test with following options:
Number of threads: 4
Initializing random number generator from current time


Initializing worker threads...

Threads started!

SQL statistics:
    queries performed:
        read:                            300118
        write:                           85615
        other:                           42826
        total:                           428559
    transactions:                        21389  (356.13 per sec.)
    queries:                             428559 (7135.48 per sec.)
    ignored errors:                      48     (0.80 per sec.)
    reconnects:                          0      (0.00 per sec.)

General statistics:
    total time:                          60.0543s
    total number of events:              21389

Latency (ms):
         min:                                    2.27
         avg:                                   11.23
         max:                                  225.41
         95th percentile:                       51.94
         sum:                               240122.50

Threads fairness:
    events (avg/stddev):           5347.2500/4.97
    execution time (avg/stddev):   60.0306/0.00
```
