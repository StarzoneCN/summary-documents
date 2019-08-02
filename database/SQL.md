# <div style="text-align:center;color:#FF9900">SQL</div>

## 1. DML & DDL
### 1.1 [TRUNCATE][TRUNCATE-SYNTAX]
*清空表格数据，类似`delete from tablename`*
### 1.1.1 语法
```SQL
TRUNCATE TABLE table_name;  
```

### 1.1.2 vs DELETE TABLE
更快、更少资源占用
> Truncate table is faster and uses lesser resources than DELETE TABLE command.

### 1.1.2 vs DROP TABLE
`DROP TABLE`会删除表结构，`TRUNCATE TABLE`不会；

### 1.1.3 注意
一旦使用`TRUNCATE TABLE`，无法回滚
> The rollback process is not possible after truncate table statement. Once you truncate a table you cannot use a flashback table statement to retrieve the content of the table.


### 2. 事务
#### 2.1 悲观锁和乐观锁
##### 2.1.1 悲观锁


##### 2.1.2 乐观锁
> 乐观锁，大多是基于数据版本（ Version ）记录机制实现。何谓数据版本？即为数据增加一个版本标识，在基于数据库表的版本解决方案中，一般是通过为数据库表增加一个 “version” 字段来实现。读取出数据时，将此版本号一同读出，之后更新时，对此版本号加一。此时，将提交数据的版本数据与数据库表对应记录的当前版本信息进行比对，如 果提交的数据版本号大于数据库表当前版本号，则予以更新，否则认为是过期数据。

#### 2.2 ACID
参考：[数据库事务的四大特性（ACID）][]






[TRUNCATE-SYNTAX]: https://www.javatpoint.com/sql-truncate-table
[数据库事务的四大特性（ACID）]: https://blog.csdn.net/qq_25448409/article/details/78110430
