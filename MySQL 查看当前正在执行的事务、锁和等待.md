# MySQL 查看当前正在执行的事务、锁和等待

## 查看事务

```sql
-- 查看当前都有哪些事务
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_TRX\G;
*************************** 1. row ***************************
                    trx_id: 1628255 -- 当前事务ID
                 trx_state: LOCK WAIT -- 事务状态：锁等待
               trx_started: 2021-10-11 16:42:34
     trx_requested_lock_id: 1628255:9585:3:2 -- 欲请求的锁
          trx_wait_started: 2021-10-11 16:42:44
                trx_weight: 2
       trx_mysql_thread_id: 3 -- processlist 中的线程ID
                 trx_query: select * from account where id  = 1 for update
       trx_operation_state: starting index read
         trx_tables_in_use: 1
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 1
         trx_rows_modified: 0
   trx_concurrency_tickets: 0
       trx_isolation_level: READ COMMITTED
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
*************************** 2. row ***************************
                    trx_id: 1628254
                 trx_state: RUNNING
               trx_started: 2021-10-11 16:42:32
     trx_requested_lock_id: NULL
          trx_wait_started: NULL
                trx_weight: 2
       trx_mysql_thread_id: 24
                 trx_query: NULL
       trx_operation_state: NULL
         trx_tables_in_use: 0
         trx_tables_locked: 1
          trx_lock_structs: 2
     trx_lock_memory_bytes: 1136
           trx_rows_locked: 1
         trx_rows_modified: 0
   trx_concurrency_tickets: 0
       trx_isolation_level: REPEATABLE READ
         trx_unique_checks: 1
    trx_foreign_key_checks: 1
trx_last_foreign_key_error: NULL
 trx_adaptive_hash_latched: 0
 trx_adaptive_hash_timeout: 0
          trx_is_read_only: 0
trx_autocommit_non_locking: 0
2 rows in set (0.00 sec)

ERROR: 
No query specified
```

## 查看锁

```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCKS\G;
*************************** 1. row ***************************
    lock_id: 1628257:9585:3:2 -- 当前锁ID
lock_trx_id: 1628257 -- 该锁对应的事务ID
  lock_mode: X -- 锁类型，排它锁
  lock_type: RECORD -- 锁范围
 lock_table: `test`.`account`
 lock_index: PRIMARY
 lock_space: 9585
  lock_page: 3
   lock_rec: 2
  lock_data: 1
*************************** 2. row ***************************
    lock_id: 1628256:9585:3:2
lock_trx_id: 1628256
  lock_mode: X
  lock_type: RECORD
 lock_table: `test`.`account`
 lock_index: PRIMARY
 lock_space: 9585
  lock_page: 3
   lock_rec: 2
  lock_data: 1
2 rows in set, 1 warning (0.00 sec)

ERROR: 
No query specified
```

## 查看锁等待

```sql
mysql> SELECT * FROM INFORMATION_SCHEMA.INNODB_LOCK_WAITS\G;
*************************** 1. row ***************************
requesting_trx_id: 1628259 -- 请求锁的事务ID（等待方）
requested_lock_id: 1628259:9585:3:2 -- 请求的锁ID
  blocking_trx_id: 1628258 -- 阻塞该锁的事务ID（当前持有方，待释放）
 blocking_lock_id: 1628258:9585:3:2 -- 持有的锁ID
1 row in set, 1 warning (0.00 sec)

ERROR: 
No query specified
```

## 查看进程

```sql
mysql> SHOW PROCESSLIST\G;

mysql> KILL 进程ID;
```