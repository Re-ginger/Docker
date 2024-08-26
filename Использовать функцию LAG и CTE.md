Для начала создадим таблицу statistic: 
```bash 
CREATE TABLE statistic (
    player_name VARCHAR(100) NOT NULL,
    player_id INT NOT NULL,
    year_game SMALLINT NOT NULL CHECK (year_game > 0),
    points DECIMAL(12,2) CHECK (points >= 0),
    PRIMARY KEY (player_name, year_game)
);
```
Теперь добавим данные в таблицу:
```bash 
INSERT INTO statistic (player_name, player_id, year_game, points) VALUES
('Mike', 1, 2018, 18),
('Jack', 2, 2018, 14),
('Jackie', 3, 2018, 30),
('Jet', 4, 2018, 30),
('Luke', 1, 2019, 16),
('Mike', 2, 2019, 14),
('Jack', 3, 2019, 15),
('Jackie', 4, 2019, 28),
('Jet', 5, 2019, 25),
('Luke', 1, 2020, 19),
('Mike', 2, 2020, 17),
('Jack', 3, 2020, 18),
('Jackie', 4, 2020, 29),
('Jet', 5, 2020, 27);
```

Запрос суммы очков с группировкой и сортировкой по годам:
```bash 
WITH YearlyStats AS (SELECT year_game, SUM(points) AS total_points FROM statistic GROUP BY year_game) SELECT * FROM YearlyStats ORDER BY year_game;
+-----------+--------------+
| year_game | total_points |
+-----------+--------------+
|      2018 |        92.00 |
|      2019 |        98.00 |
|      2020 |       110.00 |
+-----------+--------------+
```

Для вывода количества очков за текущий год и предыдущий с помощью функции LAG, воспользуемся следующим запросом:
```bash 
WITH PlayerPoints AS (SELECT player_name, year_game, SUM(points) AS total_points FROM statistic GROUP BY player_name, year_game) SELECT player_name,  year_game, total_points AS current_year_points, LAG(total_points) OVER (PARTITION BY player_name ORDER BY year_game) AS previous_year_points FROM PlayerPoints ORDER BY player_name, year_game;

+-------------+-----------+---------------------+----------------------+
| player_name | year_game | current_year_points | previous_year_points |
+-------------+-----------+---------------------+----------------------+
| Jack        |      2018 |               14.00 |                 NULL |
| Jack        |      2019 |               15.00 |                14.00 |
| Jack        |      2020 |               18.00 |                15.00 |
| Jackie      |      2018 |               30.00 |                 NULL |
| Jackie      |      2019 |               28.00 |                30.00 |
| Jackie      |      2020 |               29.00 |                28.00 |
| Jet         |      2018 |               30.00 |                 NULL |
| Jet         |      2019 |               25.00 |                30.00 |
| Jet         |      2020 |               27.00 |                25.00 |
| Luke        |      2019 |               16.00 |                 NULL |
| Luke        |      2020 |               19.00 |                16.00 |
| Mike        |      2018 |               18.00 |                 NULL |
| Mike        |      2019 |               14.00 |                18.00 |
| Mike        |      2020 |               17.00 |                14.00 |
+-------------+-----------+---------------------+----------------------+
```
