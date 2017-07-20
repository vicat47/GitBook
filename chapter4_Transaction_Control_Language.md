# 事务控制语言
**事务控制语言(Transactions Control Language,TCL)** 的语句能 **确保被DML语句影响的表的所有行及时得以更新**。TCL语句包括BEGIN TRANSACTION，COMMIT和ROLLBACK。Oracle数据库确保基于事务的数据一致性。在更改数据时可以更加灵活和控制，并确保用户进程故障或系统故障时的数据一致性。事务由构成数据一致变化的DML语句组成。
## 事务的构成
一个数据库事务由以下对象之一组成:
- 使用DML语句对数据进行一次一致更改
- 一条DDL语句
- 一条DCL语句
## 事务的开始和结束
- 当遇到第一个DML语句时，事务开始
- 当出现以下情况之一时结束：
  - 发出COMMIT或ROLLBACK语句。
  - 发出DDL语句，如CREATE。
  - 发出DCL声明。
  - 用户退出SQL Developer或SQL * Plus。
  - 机器发生故障或系统崩溃。
- 一个事务结束后，下一个可执行SQL语句将自动启动下一个事务。
- DDL语句或DCL语句自动提交，因此隐式结束事务。
## COMMIT 和 ROLLBACK 语句
### 作用
- 确保数据的一致性
- 在使更改永久化之前，可以预览数据的更改
- 按逻辑关系对相关操作进行分组
![事务控制](https://ws1.sinaimg.cn/large/e50fdd0bly1fhqjkrkapnj20gm09iq2z.jpg)
### SAVEPOINT 与 ROLLBACK
- 使用SAVEPOINT语句在当前事务处理中创建一个标记.
- 使用ROLLBACK TO SAVEPOINT 语句回退到该标记处
```sql
UPDATE...
SAVEPOINT update_done;
INSERT...
ROLLBACK TO update_done;
```
- 如果之前有同名的存储点，则之后的存储点会**覆盖**之前同名的存储点
### 隐式的事务处理
- 在一下情况Oracle会自动提交:
  - 发出DDL语句
  - 发出DCL语句
  - 正常退出SQL Developer 或 SQL PLUS而没有显示的发出COMMIT或者ROLLBACK语句
- 在SQL Developer 或 SQL Plus 异常终止或系统发生故障的情况下，自动回退。
> 在SQL * Plus中，可以设置AUTOCOMMIT命令ON或OFF。 如果设置为ON，则每个单独的DML语句都将在执行时立即提交。 无法回滚更改。 如果设置为OFF，则COMMIT语句仍然可以被明确发出。 此外，COMMIT语句是在发出DDL语句或退出SQL * Plus时发出的。 在SQL Developer中跳过SET AUTOCOMMIT ON / OFF命令。 只有启用了Autocommit首选项后，DML才会在SQL Developer的正常退出时执行。 要启用自动提交功能，请执行以下操作：
> 在工具菜单中，选择首选项。 在首选项对话框中，展开数据库并选择工作表参数。  
> 在右窗格中，选择“SQL工作表中的自动提交”选项。 单击确定。
