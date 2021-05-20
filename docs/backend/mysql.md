# MySql

### 一、Linux 下 MySql 安装 & 修改密码

1. 更新软件包
   ```
   apt update
   ```
2. 安装 `mysql`
   ```
   apt install mysql-server mysql-common mysql-client
   ```
3. 进入 `mysql`
   ```
   mysql
   ```
4. 查询 `mysql` 密码状态
   ```
   select user,plugin from mysql.user;
   ```
5. 更改 `mysql root` 账户认证模式
   ```
   update mysql.user set authentication_string=PASSWORD('密码'), plugin='mysql_native_password' where user='root';flush privileges;
   ```
6. 退出 `mysql` 终端
   ```
   exit
   ```
7. 重启 `mysql`
   ```
   service mysql restart
   ```
8. 登录 `mysql`，
   ```
   mysql
   ```
   提示 `Access denied for user 'root'@'localhost' (using password: NO)`，说明密码修改成功
9. 使用账号密码登录
   ```
   mysql -u root -p密码
   ```

### 二、开启远程登录

1. 配置 `root` 远程登录，使用 `root` 账户进入 `mysql`，输入
   ```
   grant all on *.* to root@'%' identified by '密码' with grant option;
   flush privileges;
   ```
2. 修改侦测地址
   ```
   vim /etc/mysql/mysql.conf.d/mysqld.cnf
   ```
   或者
   ```
   vim /etc/mysql/mariadb.conf.d/50-server.cnf
   ```
   输入 `shift+;` 在 `vim` 命令行模式下输入 `/bind-address` ，查找 `bind-address=127.0.0.1`，将默认绑定的内网地址 `127.0.0.1` 改为 `0.0.0.0`，保存并退出
3. 重启 `mysql`
   ```
   service mysql restart
   ```
4. 使用本地机器，验证是否可以远程登录(需要本地已经安装过 `mysql`)
   ```
   mysql -h 远程服务器地址 -u root -p密码
   ```

### 三、 常用命令

##### 管理 MySQL

| command                                                                | description                                                      |
| ---------------------------------------------------------------------- | ---------------------------------------------------------------- |
| _ 数据库 _                                                             |                                                                  |
| SHOW DATABASES                                                         | 列出所有数据库                                                   |
| CREATE DATABASE name                                                   | 创建一个新数据库                                                 |
| DROP DATABASE name                                                     | 删除一个数据库                                                   |
| USE name                                                               | 切换到一个数据库                                                 |
| _ 表 _                                                                 |                                                                  |
| SHOW TABLES                                                            | 列出当前数据库的所有表                                           |
| DESC name                                                              | 查看一个表的结构                                                 |
| SHOW CREATE TABLE name                                                 | 查看创建表的 SQL 语句                                            |
| DROP TABLE name                                                        | 创建表                                                           |
| DROP TABLE name                                                        | 删除表                                                           |
| ALTER TABLE students ADD COLUMN birth VARCHAR(10) NOT NULL             | 给`students`表新增一列`birth`                                    |
| ALTER TABLE students CHANGE COLUMN birth birthday VARCHAR(20) NOT NULL | 要修改`birth`列，例如把列名改为`birthday`，类型改为`VARCHAR(20)` |
| ALTER TABLE students DROP COLUMN birthday                              | 删除列                                                           |

##### 实用 SQL 语句

插入或替换

> 如果我们希望插入一条新记录（INSERT），但如果记录已经存在，就先删除原记录，再插入新记录。此时，可以使用 REPLACE 语句，这样就不必先查询，再决定是否先删除再插入,若 id=1 的记录不存在，REPLACE 语句将插入新记录，否则，当前 id=1 的记录将被删除，然后再插入新记录：

```
REPLACE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

插入或更新

> 如果我们希望插入一条新记录（INSERT），但如果记录已经存在，就更新该记录，此时，可以使用 INSERT INTO ... ON DUPLICATE KEY UPDATE ...语句，若 id=1 的记录不存在，INSERT 语句将插入新记录，否则，当前 id=1 的记录将被更新，更新的字段由 UPDATE 指定。

```
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99;
```

插入或忽略

> 如果我们希望插入一条新记录（INSERT），但如果记录已经存在，就啥事也不干直接忽略，此时，可以使用 INSERT IGNORE INTO ...语句，若 id=1 的记录不存在，INSERT 语句将插入新记录，否则，不执行任何操作。

```
INSERT IGNORE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

快照

> 如果想要对一个表进行快照，即复制一份当前表的数据到一个新表，可以结合 CREATE TABLE 和 SELECT，新创建的表结构和 SELECT 使用的表结构完全一致。

```
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;
```

写入查询结果集

> 如果查询结果集需要写入到表中，可以结合 INSERT 和 SELECT，将 SELECT 语句的结果集直接插入到指定表中。

```
INSERT INTO statistics (class_id, average) SELECT class_id, AVG(score) FROM students GROUP BY class_id;
```

强制使用指定索引

> 在查询的时候，数据库系统会自动分析查询语句，并选择一个最合适的索引。但是很多时候，数据库系统的查询优化器并不一定总是能使用最优索引。如果我们知道如何选择索引，可以使用 FORCE INDEX 强制查询使用指定的索引。例如：

```
SELECT * FROM students FORCE INDEX (idx_class_id) WHERE class_id = 1 ORDER BY id DESC;
```

##### 查询数据

| command                                                                    | description                                                         |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| _ 基本查询 _                                                               |                                                                     |
| SELECT \* FROM students;                                                   | 查询 students 表的所有数据                                          |
| SELECT 1;                                                                  | 不带`FROM`子句的`SELECT`语句有一个有用的用途，                      |
|                                                                            | 就是用来判断当前到数据库的连接是否有效。                            |
|                                                                            | 许多检测工具会执行一条`SELECT 1;`                                   |
|                                                                            | 来测试数据库连接                                                    |
| _ 条件查询 _                                                               |                                                                     |
| SELECT \* FROM <表名> WHERE <条件表达式>                                   | SELECT \* FROM students WHERE score >= 80; 分数在 80 分或以上的学生 |
| SELECT \* FROM <条件 1> AND <条件 2>                                       | SELECT \* FROM students WHERE score >= 80 AND gender = 'M';         |
| SELECT \* FROM <条件 1> OR<条件 2>                                         | SELECT \* FROM students WHERE score >= 80 OR gender = 'M';          |
| SELECT \* FROM NOT <条件>                                                  | SELECT \* FROM students WHERE NOT class_id = 2;                     |
| SELECT \* FROM students WHERE (score < 80 OR score > 90) AND gender = 'M'; | 分数在 80 以下或者 90 以上，并且是男生                              |
| 优先级                                                                     | NOT > AND > OR                                                      |
| _ 常用的条件表达式 _                                                       |                                                                     |
| 使用=判断相等                                                              | score = 80                                                          |
| 使用>判断大于                                                              | name > 'abc' (字符串比较根据 ASCII 码，中文字符比较根据数据库设置)  |
| 使用>=判断大于或相等                                                       | score >= 80                                                         |
| 使用<判断小于                                                              | score < 80                                                          |
| 使用<=判断小于或相等                                                       | name <= 'abc'                                                       |
| 使用<>判断不相等                                                           | score <> 80                                                         |
| 使用 LIKE 判断相似                                                         | name LIKE 'ab%'                                                     |
|                                                                            | name LIKE '%bc%'                                                    |
|                                                                            | ( %表示任意字符，例如'ab%'将匹配'ab'，'abc'，'abcd' )               |
| _ 投影查询 _                                                               |                                                                     |
| SELECT id, score, name FROM students;                                      | 从`students`表中返回`id`、`score`和`name`这三列                     |
| SELECT students.id no FROM students;                                       | 给列设置别名，将查询出来的 id 列，列名由 id 改为 no                 |

- 排序

按照成绩从低到高进行排序

```
SELECT id, name, gender, score FROM students ORDER BY score;
```

按照成绩从高到底排序，加上 DESC 表示“倒序”

```
SELECT id, name, gender, score FROM students ORDER BY score DESC;
```

使用 ORDER BY score DESC, gender 表示先按 score 列倒序，如果有相同分数的，再按 gender 列排序

```
SELECT id, name, gender, score FROM students ORDER BY score DESC, gender;
```

如果有 WHERE 子句，那么 ORDER BY 子句要放到 WHERE 子句后面

```
SELECT id, name, gender, score
FROM students
WHERE class_id = 1
ORDER BY score DESC;
```

- 分页

  > 从结果集中“截取”出第 M~N 条记录，这个查询可以通过`LIMIT <M> OFFSET <N>`子句实现。  
  > OFFSET 是可选的，如果只写 LIMIT 15，那么相当于 LIMIT 15 OFFSET 0。  
  > 在 MySQL 中，LIMIT 15 OFFSET 30 还可以简写成 LIMIT 30, 15。
  > 使用 LIMIT `<M>` OFFSET `<N>`分页时，随着 N 越来越大，查询效率也会越来越低。

- 聚合查询
  查询 students 表一共有多少条记录，COUNT(\*)表示查询所有列的行数：

```
SELECT COUNT(*) FROM students;
```

使用聚合查询并设置结果集的列名为 num:

```
SELECT COUNT(*) num FROM students;
```

使用聚合查询并设置 WHERE 条件:

```
SELECT COUNT(*) boys FROM students WHERE gender = 'M';
```

除了 COUNT()函数外，SQL 还提供了如下聚合函数：  
|函数|说明|
|-|-|
|SUM|计算某一列的合计值，该列必须为数值类型|
|AVG|计算某一列的平均值，该列必须为数值类型|
|MAX|计算某一列的最大值|
|MIN|计算某一列的最小值|

> MAX()和 MIN()函数并不限于数值类型。如果是字符类型，MAX()和 MIN()会返回排序最后和排序最前的字符。
> 使用聚合查询计算男生平均成绩:

```
SELECT AVG(score) average FROM students WHERE gender = 'M';
```

对于聚合查询，SQL 还提供了“分组聚合”的功能：

```
SELECT class_id，COUNT(*) num FROM students GROUP BY class_id;
```

查出每个班级的平均分：

```
SELECT * FROM students;
```

查出每个班级男生和女生的平均分：

```
SELECT class_id, gender, AVG(score) FROM students GROUP BY class_id, gender;
```

- 多表查询

```
SELECT * FROM classes, students;
```

- 连接查询
  > 连接查询是另一种类型的多表查询。连接查询对多个表进行 JOIN 运算，简单地说，就是先确定一个主表作为结果集，然后，把其他表的行有选择性地“连接”在主表结果集上，分为内连接查询和外连接查询

内连接查询 INNER JOIN：

1. 先确定主表，仍然使用 FROM <表 1>的语法；
2. 再确定需要连接的表，使用 INNER JOIN <表 2>的语法；
3. 然后确定连接条件，使用 ON <条件...>，这里的条件是 s.class_id = c.id，表示 students 表的 class_id 列与 classes 表的 id 列相同的行需要连接；
4. 可选：加上 WHERE 子句、ORDER BY 等子句。

```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id;
```

外连接查询 RIGHT OUTER JOIN & LEFT OUTER JOIN & FULL OUTER JOIN：

> INNER JOIN 只返回同时存在于两张表的行数据，由于 students 表的 class_id 包含 1，2，3，classes 表的 id 包含 1，2，3，4，所以，INNER JOIN 根据条件 s.class_id = c.id 返回的结果集仅包含 1，2，3。

> RIGHT OUTER JOIN 返回右表都存在的行。如果某一行仅在右表存在，那么结果集就会以 NULL 填充剩下的字段。

> LEFT OUTER JOIN 则返回左表都存在的行。如果我们给 students 表增加一行，并添加 class_id=5，由于 classes 表并不存在 id=5 的行，所以，LEFT OUTER JOIN 的结果会增加一行，对应的 class_name 是 NULL

```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
LEFT OUTER JOIN classes c
ON s.class_id = c.id;
```

```
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
FULL OUTER JOIN classes c
ON s.class_id = c.id;
```

假设查询语句为`SELECT ... FROM tableA ??? JOIN tableB ON tableA.column1 = tableB.column2;`

我们把 tableA 看作左表，把 tableB 看成右表，那么 INNER JOIN 是选出两张表都存在的记录：

![1.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/684ab0cacd44489d9bd5c9fd6124f594~tplv-k3u1fbpfcp-watermark.image)

LEFT OUTER JOIN 是选出左表存在的记录：

![2.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cf1021ff12f04b05b7f44100ad11c431~tplv-k3u1fbpfcp-watermark.image)  
RIGHT OUTER JOIN 是选出右表存在的记录：

![3.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b8fca5fb353a4172a0b87f55f6e56bfb~tplv-k3u1fbpfcp-watermark.image)  
FULL OUTER JOIN 则是选出左右表都存在的记录：

![4.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d92cc6a239444ec88ef5595edf8557e2~tplv-k3u1fbpfcp-watermark.image)

##### 修改数据

- 插入数据 `INSERT`

```
INSERT INTO students (class_id, name, gender, score) VALUES
  (1, '大宝', 'M', 87),
  (2, '二宝', 'M', 81);
```

- 更新数据 `UPDATE`

```
UPDATE student SET sname='AA' WHERE sno=10011;
```

把所有 80 分以下的同学的成绩加 10 分

```
UPDATE students SET score=score+10 WHERE score<80;
```

- 删除数据 `DELETE`

```
DELETE FROM student WHERE sno >= 10010;
```

### 四、事务

在执行 SQL 语句的时候，某些业务要求，一系列操作必须全部执行，而不能仅执行一部分。把多条 SQL 语句作为一个整体进行操作的功能，被称为数据库事务。数据库事务可以确保该事务范围内的所有操作都可以全部成功或者全部失败。如果事务失败，那么效果就和没有执行这些 SQL 一样，不会对数据库数据有任何改动。

> 数据库事务具有 ACID 这 4 个特性：
>
> - A：Atomic，原子性，将所有 SQL 作为原子工作单元执行，要么全部执行，要么全部不执行
> - C：Consistent，一致性，事务完成后，所有数据的状态都是一致的，即 A 账户只要减去了 100，B 账户则必定加上了>100
> - Isolation，隔离性，如果有多个事务并发执行，每个事务作出的修改必须与其他事务隔离
> - Duration，持久性，即事务完成后，对数据库数据的修改被持久化存储

- 隐式事务：对于单条 SQL 语句，数据库系统自动将其作为一个事务执行，这种事务被称为隐式事务
- 显式事务：要手动把多条 SQL 语句作为一个事务执行，使用`BEGIN`开启一个事务，使用`COMMIT`提交一个事务，这种事务被称为显式事务

```mysql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

`COMMIT`是指提交事务，即试图把事务内的所有 SQL 所做的修改永久保存。如果`COMMIT`语句执行失败了，整个事务也会失败。
有些时候，我们希望主动让事务失败，这时，可以用 ROLLBACK 回滚事务，整个事务会失败：

```mysql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
ROLLBACK;
```

- 隔离级别

对于两个并发执行的事务，如果涉及到操作同一条记录的时候，可能会发生问题。因为并发操作会带来数据的不一致性，包括脏读、不可重复读、幻读等。数据库系统提供了隔离级别来让我们有针对性地选择事务的隔离级别，避免数据不一致的问题。  
SQL 标准定义了 4 种隔离级别，分别对应可能出现的数据不一致的情况：

| Isolation Level  | 脏读（Dirty Read） | 不可重复读（Non Repeatable Read） | 幻读（Phantom Read） |
| ---------------- | ------------------ | --------------------------------- | -------------------- |
| Read Uncommitted | Yes                | Yes                               | Yes                  |
| Read Committed   | -                  | Yes                               | Yes                  |
| Repeatable Read  | -                  | -                                 | Yes                  |
| Serializable     | -                  | -                                 | -                    |

#### Read Uncommitted

Read Uncommitted 是隔离级别最低的一种事务级别。在这种隔离级别下，一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据，这就是脏读（Dirty Read）。

#### Read Committed

在 Read Committed 隔离级别下，一个事务可能会遇到不可重复读（Non Repeatable Read）的问题。  
不可重复读是指，在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致。

#### Repeatable Read

在 Repeatable Read 隔离级别下，一个事务可能会遇到幻读（Phantom Read）的问题。

幻读是指，在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了。

#### Serializable

Serializable 是最严格的隔离级别。在 Serializable 隔离级别下，所有事务按照次序依次执行，因此，脏读、不可重复读、幻读都不会出现。  
虽然 Serializable 隔离级别下的事务具有最高的安全性，但是，由于事务是串行执行，所以效率会大大下降，应用程序的性能会急剧降低。如果没有特别重要的情景，一般都不会使用 Serializable 隔离级别。

- 默认隔离级别
  如果没有指定隔离级别，数据库就会使用默认的隔离级别。在 MySQL 中，如果使用 InnoDB，默认的隔离级别是 Repeatable Read。

—— END ——
