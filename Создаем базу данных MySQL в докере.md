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
