### 1. Tабличное пространство в PostgreSQL
```sql
postgres=# CREATE TABLESPACE ts_data LOCATION '/var/lib/postgresql/ts_data';
CREATE TABLESPACE
postgres=# CREATE TABLESPACE ts_indexes LOCATION '/var/lib/postgresql/ts_indexes';
CREATE TABLESPACE
postgres=# \db
                                  List of tablespaces
         Name         |  Owner   |                       Location
----------------------+----------+------------------------------------------------------
 ts_data              | postgres | /var/lib/postgresql/ts_data
 ts_indexes           | postgres | /var/lib/postgresql/ts_indexes
(2 rows)
```

```sql
store=# CREATE ROLE role1 WITH LOGIN PASSWORD '111';
CREATE ROLE
store=# CREATE USER user2 WITH PASSWORD 'my_pass';
CREATE ROLE
store=# GRANT ALL PRIVILEGES ON DATABASE "store" TO user2;
GRANT
store=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO user2;
GRANT
store=# \dt
             List of relations
  Schema   |    Name    | Type  |  Owner
-----------+------------+-------+----------
 inventory | categories | table | postgres
 inventory | products   | table | postgres
(2 rows)

store=# GRANT SELECT ON TABLE inventory.categories TO user2;
GRANT
store=#
store=# CREATE SCHEMA sales AUTHORIZATION postgres;
CREATE SCHEMA
store=# CREATE SCHEMA inventory AUTHORIZATION postgres;
CREATE SCHEMA
store=#
```

### 2. Создание схемы, таблицы и распределите их по табличным пространствам:

```sql
-- Таблицы в схеме sales
SET search_path TO sales;
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100) UNIQUE
) TABLESPACE ts_data;

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INTEGER REFERENCES sales.customers(customer_id),
    order_date DATE,
    total_amount DECIMAL(10, 2)
) TABLESPACE ts_data;

--  Таблицы в схеме inventory
SET search_path TO inventory;
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    category_id INTEGER,
    product_name VARCHAR(100),
    price DECIMAL(10, 2)
) TABLESPACE ts_data;

CREATE TABLE categories (
    category_id SERIAL PRIMARY KEY,
    category_name VARCHAR(100)
) TABLESPACE ts_indexes;
```
