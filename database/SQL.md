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






[TRUNCATE-SYNTAX]: https://www.javatpoint.com/sql-truncate-table
