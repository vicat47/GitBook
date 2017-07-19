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
