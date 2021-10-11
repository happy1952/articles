# MySQL 事务隔离级别及常见问题

## 事前准备

```sql
mysql> CREATE TABLE `city` (
    -> `id` int(10) NOT NULL AUTO_INCREMENT,
    -> `name` varchar(32) DEFAULT NULL,
    -> PRIMARY KEY (`id`)
    -> ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

mysql> INSERT INTO city (name) VALUES ("武汉市");

mysql> SELECT * FROM city WHERE id = 1 \G;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | 武汉市    |
+----+-----------+
1 row in set (0.00 sec)
```

## 事务隔离级别

我们知道 MySQL 是一个 C/S 架构的软件，即分为客户端(Client)和服务器端(Server)。对于同一个服务器端来说，可以有若干个客户端与之连接，每个客户端连接上之后，就可以称之为一个会话(Session)。每个客户端都可以在自己的会话中向服务器发出请求，一个请求语句可能是某个事务的一部分，也就是对于服务器来说可能同时处理多个事务。理论上在某个事务对某个数据进行访问时，其他事务应该进行排队，等该事务提交之后，其他事务才可以继续访问这个数据。但是这样的话对性能影响太大了，我们既想要保持事务的隔离性，又想让服务器在访问同一数据的多个事务时性能尽量高些，鱼和熊掌不可兼得，这就引出了我们下面要讲的事务的隔离级别。

### 事务并发执行遇到的问题

在开始之前，我们得先看一下访问相同数据的事务在不保证串行执行的情况下可能出现哪些问题：

* 脏写（Dirty Write）

一个事务修改了另一个未提交事务修改过的数据。如下：Session A 和 Session B 各开启了一个事务，Session B 先将 ID 为 1 的 name 列更新为 '北京市'，然后 Session A 中的事务接着又把这条 ID 为 1 的 name 列更新为 '太原市'。如果之后 Session B 中的事务进行了回滚，那么 Session A 中的更新也将不复存在，这种现象就称之为脏写。

时间线  |           Session A           | Session B
-------|-------------------------------|---------------
t1     | BEGIN;                        |
t2     |                               | BEGIN;
t3     |                               | UPDATE city SET name = '北京市' WHERE id = 1;
t4     | UPDATE city SET name = '太原市' WHERE id = 1; |
t5     | COMMIT;                       |
t6     |                               | ROLLBACK;

* 脏读（Dirty Read）

一个事务读到了另一个未提交事务修改过的数据。如下：Session A 和 Session B 各开启了一个事务，Session B 先将 ID 为 1 的 name 列更新为 '北京市'，然后 Session A 再去查询这条 ID 为 1 的记录，如果读到 name 的值为 '北京市'，而 Session B 中的事务稍后进行了回滚，那么 Session A 中的事务相当于读到了一个不存在的数据，这种现象就称为脏读。

时间线  |           Session A           | Session B
-------|-------------------------------|---------------
t1     | BEGIN;                        |
t2     |                               | BEGIN;
t3     |                               | UPDATE city SET name = '北京市' WHERE id = 1;
t4     | SELECT * FROM city WHERE id = 1; (如果读到 name 的值为北京市，则意味着发生了脏读。) |
t5     | COMMIT;                       |
t6     |                               | ROLLBACK;

* 不可重复读（Non-Repeatable Read）

一个事务只能读到另一个已提交的事务修改过的数据，并且其他事务每对该数据进行一次修改并提交后，该事务都能查询到最新值。如下：我们在 Session B 中提交了几个隐式事务（注意是隐式事务，意味着语句结束事务就提交了），这些事务都修改了 ID 为 1 的记录的列 name 的值，每次事务提交之后，如果 Session A 中的事务都可以查看到最新的值，这种现象也被称为不可重复读。

时间线  |           Session A           | Session B
-------|-------------------------------|---------------
t1     | BEGIN;                        |
t2     | SELECT * FROM city WHERE id = 1; (此时 name 值为'武汉市') |
t3     |                               | UPDATE city SET name = '北京市' WHERE id = 1;
t4     | SELECT * FROM city WHERE id = 1; (如果读到 name 的值为北京市，则意味着发生了不可重复读。) |
t5     |                              | UPDATE city SET name = '太原市' WHERE id = 1;
t6     |  SELECT * FROM city WHERE id = 1; (如果读到 name 的值为太原市，则意味着发生了不可重复读。) |

* 幻读（Phantom）

一个事务先根据某些条件查询出一些记录，之后另一个事务又向表中插入了符合这些条件的记录，原先的事务再次按照该条件查询时，能把另一个事务插入的记录也读出来。如下：Session A 中的事务先根据条件 id > 0 这个条件查询 city 表，得到 name 列值为'武汉市'的记录，之后 Session B 中提交了一个隐式事务，该事务向表 city 中插入了一条新记录，之后 Session A 中的事务再根据相同的条件 id > 0 查询 city 表，得到的结果集中包含 session B中的事务新插入的那条记录，这种现象称为幻读。

那么有人就会产生疑问，那如果 Session B 中删除了一些符合 id > 0 的记录而不是插入新记录，那 Session A 中之后再根据 id > 0 的条件读取的记录变少了，这种现象算不算幻读呢？明确说明一下，这种情况不属于幻读，幻读强调的是一个事务按照某个相同的条件多次读取记录时，后读取时读到了之前没有读到的记录。对于先前已经读到的记录，之后又读取不到这种情况，其实相当于对每一次记录都发生了不可重复读的现象。幻读只是重点强调了读取到了之前没有获取到的记录。

时间线  |           Session A           | Session B
-------|-------------------------------|---------------
t1     | BEGIN;                        |
t2     | SELECT * FROM city WHERE id > 0; (此时 name 值为'武汉市') |
t3     |                               | INSERT INTO city (name) VALUES ('北京市');
t4     | SELECT * FROM city WHERE id > 0; (如果读到 name 列的值为'武汉市'、'北京市'的记录，则意味着发生了幻读。) |

注：上面介绍了几种并发事务执行过程中可能遇到的一些问题，这些问题也有轻重缓急之分，我们给这些问题按照严重性来排一下序：

**脏写 > 脏读 > 不可重复读 > 幻读**

### SQL 标准中的四种隔离级别

* READ UNCOMMITTED: 未提交读
* READ COMMITTED: 已提交读
* REPEATABLE READ: 可重复读
* SERIALIZABLE: 可串行化 

隔离级别            |  脏读  |  不可重复读  |  幻读
-------------------|:------:|:-----------:|:-----:
READ UNCOMMITTED   |  允许  |     允许     | 允许
READ COMMITTED     | 不允许 |     允许     | 允许
REPEATABLE READ    | 不允许 |    不允许    | 允许
SERIALIZABLE       | 不允许 |    不允许    | 不允许

注：**不论哪种隔离级别都不允许出现脏写的情况发生**

### MySQL 中支持的四种隔离级别

不同数据库厂商对 SQL 标准中规定的四种隔离级别支持不一样，比如说 Oracle 就只支持 READ COMMITTED 和 SERIALIZABLE 隔离级别。MySQL 虽然支持四种隔离级别，但与 SQL 标准中所规定的各级隔离级别允许发生的问题却有些出入，MySQL 在 REPEATABLE READ 隔离级别下，是可以禁止幻读问题的发生的。

MySQL 默认的隔离级别 REPEATABLE READ，我们可以手动修改事务的隔离级别。

## 真实案例，余额多扣

### 演示 SQL

```sql
mysql> CREATE TABLE `account` (
    ->     `id` bigint(20) NOT NULL AUTO_INCREMENT,
    ->     `balance` bigint(20) DEFAULT NULL,
    ->     PRIMARY KEY (`id`)
    -> ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

mysql> INSERT INTO `account` (balance) VALUES (1000);

mysql> SELECT * FROM `account` WHERE id = 1;
+----+---------+
| id | balance |
+----+---------+
|  1 |    1000 |
+----+---------+
1 row in set (0.00 sec)
```

### SQL 执行时序

时间线  |           Session A 
-------|-------------------------------
t1     | BEGIN;
t2     | SELECT * FROM account WHERE id = 1; 查询当前账户余额
t3     | SELECT * FROM account WHERE id = 1 FOR UPDATE; 锁住当前账户
t4     | 进行其他操作
t5     | SELECT * FROM account WHERE id = 1; 查询当前账户余额
t6     | 判断扣减金额是否大于账户余额
t7     | UPDATE account SET balance = balance - 100 WHERE id = 1; 扣减余额
t8     | COMMIT;

### 并发执行下余额多扣，MySQL 事务隔离级别为 RR

注：MySQL 默认的事务隔离级别是 REPEATABLE READ (可重复读)。

```sql
-- 查看事务隔离级别
SHOW VARIABLES LIKE 'transaction_isolation';

-- 或者
SELECT @@transaction_isolation;
```

时间线  |           Session A            | Session B
-------|--------------------------------|---------------
t0     | BEGIN;                         |
t1     |                                | BEGIN;
t2     | SELECT * FROM account WHERE id = 1; (balance = 1000) | 
t3     |                                | SELECT * FROM account WHERE id = 1; (balance = 1000)
t4     | SELECT * FROM account WHERE id = 1 FOR UPDATE; (balance = 1000) | 
t5     |                                | SELECT * FROM account WHERE id = 1 FOR UPDATE; (阻塞)
t6     | SELECT * FROM account WHERE id = 1; (balance = 1000) | 
t7     |                                |
t8     | UPDATE account SET balance = balance - 100 WHERE id = 1; (balance = 900) | 
t9     | COMMIT;                        | 
t10    |                                | (Session A 提交事务后，Session B 获得锁 balance = 900)
t11    |                                | SELECT * FROM account WHERE id = 1; (balance = 1000 RR隔离级别下不允许出现不可重复读)
t12    |                                | UPDATE account SET balance = balance - 100 WHERE id = 1; (balance = 800)
t13    |                                | COMMIT;

### 解决并发执行下余额多扣问题，修改 MySQL 事务隔离级别为 RC

```sql
-- 全局范围有效
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- 针对当前会话有效
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- 仅对执行后的下一个事务产生影响
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

时间线  |           Session A            | Session B
-------|--------------------------------|---------------
t0     | BEGIN;                         |
t1     |                                | BEGIN;
t2     | SELECT * FROM account WHERE id = 1; (balance = 1000) | 
t3     |                                | SELECT * FROM account WHERE id = 1; (balance = 1000)
t4     | SELECT * FROM account WHERE id = 1 FOR UPDATE; (balance = 1000) | 
t5     |                                | SELECT * FROM account WHERE id = 1 FOR UPDATE; (阻塞)
t6     | SELECT * FROM account WHERE id = 1; (balance = 1000) | 
t7     |                                |
t8     | UPDATE account SET balance = balance - 100 WHERE id = 1; (balance = 900) | 
t9     | COMMIT;                        | 
t10    |                                | (Session A 提交事务后，Session B 获得锁 balance = 900)
t11    |                                | SELECT * FROM account WHERE id = 1; (balance = 900 RC隔离级别，读已提交)
t12    |                                | UPDATE account SET balance = balance - 100 WHERE id = 1; (balance = 800)
t13    |                                | COMMIT;

