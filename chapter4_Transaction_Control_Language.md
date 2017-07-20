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
#### 关于自动提交
在SQL * Plus中，可以设置 **AUTOCOMMIT** 命令ON或OFF。 如果设置为ON，则每个单独的DML语句都将在**执行时立即提交**。 **无法回滚更改**。 如果设置为OFF，则COMMIT语句仍然可以被明确发出。 此外，COMMIT语句是在发出DDL语句或退出SQL * Plus时发出的。 在SQL Developer中跳过SET AUTOCOMMIT ON / OFF命令。 只有启用了Autocommit首选项后，DML才会在SQL Developer的正常退出时执行。 要启用自动提交功能，请执行以下操作：
- 在工具菜单中，选择首选项。 在首选项对话框中，展开数据库并选择工作表参数。
- 在右窗格中，选择“SQL工作表中的自动提交”选项。 单击确定。
#### 系统故障
- 当事务被系统故障中断时，整个事务将*自动回滚*。 这可以防止错误对数据造成不必要的更改，并将表返回到上次提交时的状态。 以这种方式，Oracle服务器保护表的完整性。
- 在SQL Developer中，通过从“文件”菜单中选择“退出”来实现会话的正常退出。 在SQL * Plus中，通过在提示符处输入EXIT命令来实现正常退出。 关闭窗口被解释为异常退出。
### COMMIT 或 ROLLBACK 之前的状态
- 在事务进行时的每个数据更改都是**暂时**的，直到事务被COMMIT。
- 数据处理操作主要影响**数据库缓冲区**， 因此，可以恢复以前的数据状态。
- 当前用户可以使用SELECT语句预览DML操作的结果.
- 其他用户**无法查看**当前用户进行的DML的结果。 Oracle拥有读取一致性，以确保每个用户看到**最后一次提交时存在**的数据。
- 受影响的行会被**锁定**，其它用户无法更改这些行中的数据.
### COMMIT 操作之后的数据的状态
- 数据库中数据的更改被持久化.
- 该数据以前的状态被覆盖.
- 所有用户都可以查看结果.
- 受影响的行的锁定会被释放，其它用户可以地这些行进行处理.
- 所有保存点都会清除.
### ROLLBACK 之后的状态
- 使用ROLLBACK语句放弃所有待定的更改:
  - 数据更改被撤消.
  - 数据还原到被修改以前的状态.
  - 受影响的行的锁定被释放.
### 举个栗子
```sql
COMMIT
DELETE FROM departments
  WHERE  department_id IN (290, 300);
SAVEPOINT deletes
UPDATE  employees
  SET   department_id = 80
  WHERE employee_id = 206;
ROLLBACK deletes
COMMIT;
```
### 语句级别的回退
- 如果在执行事务期间单个DML语句失败，则其效果可被语句级别的回滚消除，但事务中之前的DML语句所做的更改不会被丢弃。它们可以由用户明确提交或回滚。
- Oracle服务器在任何DDL语句之前和之后发出隐式提交。因此，即使您的DDL语句无法成功执行，因为服务器发出提交，因此无法回滚上一条语句。
- 应该通过执行COMMIT或ROLLBACK语句来显式终止事务。
## 读一致性
- 数据库用户以两种方式访问数据库：
  - 读操作(SELECT语句)
  - 写操作(INSERT，UPDATE，DELETE语句)
- 所以需要阅读一致性，确保以下情况：
  - 确保数据库读写器能够一致地查看数据。
  - 读者不会查看正在更改的数据。
  - 确保修改者能够以一致的方式完成对数据库的更改。
  - 一位修改者的改变不会扰乱或与另一位作家所做的改变相冲突。
- 读取一致性的目的是确保每个用户在DML操作开始之前**看到最后一次提交时存在的数据**。
> 同一用户可以登录到不同的会话。 每个会话以上述方式保持读取一致性，即使它们是相同的用户。
- 简单的说就是
  - 读不必等待写
  - 写不必等待读
  - 写等待写
### 实现
- 读一致性是一种自动实现。它将数据库的部分副本保留在撤销段中。读一致是从表中提交的数据和正在更改的还没有从撤销段提交的旧数据构建的。
- 当在数据库上进行插入，更新或删除操作时，Oracle服务器在更改数据之前会获取数据副本，并将其写入撤销段。
- 所有读者，发布变更的读者除外，在更改开始之前查看数据库;他们查看undo段的“快照”数据。
- 在更改提交到数据库之前，只有正在修改数据的用户才能看到具有更改的数据库。其他人都可以在undo段看到快照。这保证了读者对数据的读取不一致的数据当前不会发生变化。
- 当提交DML语句时，对提交完成后发出SELECT语句的任何人都可以看到对数据库所做的更改。 undo段文件中旧数据占用的空间被释放以便重新使用。
- 如果事务被回滚，更改将被撤销：
  - 原始的，旧版本的数据在undo段被写回表中。
  - 所有用户在事务开始之前看到数据库的存在。
![](https://ws1.sinaimg.cn/large/e50fdd0bly1fhqlar9e6dj20o40ht415.jpg)
## SELECT 中 FOR UPDATE 语句
- 当对数据库发出SELECT语句以查询某些记录时，所选行上不会放置任何锁。在任何给定时间锁定的记录数量（默认情况下）保持为绝对最小值：只有已更改但尚未提交的记录被锁定。
- 当发出SELECT ... FOR UPDATE语句时，关系数据库管理系统（RDBMS）会自动在SELECT语句标识的所有行上自动获得独占的行级锁，从而保存记录“仅供您进行更改”。
- 只有ROLLBACK 或 COMMIT后，锁才会被释放.
- 如果 SELECT 语句试图锁定的这行，正在被其它用户锁定，数据库将等待，直到返回结果.
- 可以将可选关键字NOWAIT附加到FOR UPDATE子句中，以告知Oracle服务器不要等待表被其他用户锁定。在这种情况下，控件将立即返回到您的程序或SQL Developer环境中，以便您可以执行其他工作，或者只是等待一段时间才能再次尝试。如果没有NOWAIT子句，则当通过发出COMMIT或ROLLBACK命令释放锁时，您的进程将阻塞，直到该表可用。
### 用于多表查询
- FOR UPDATE 子句可以用在多表SELECT查询中.
- EMPLOYEES 和 DEPARTMENTS 表中对应的行，同时被锁定.
- 使用FOR UPDATE子句，来限制我们打算更改的行，直到修改完成，否则，FOR UPDATE子句会一直锁定这些行，其它用户不能更改这些行，但是可以查询他们.
### 等待一定时间
在FOR UPDATE 后添加 WAIT 时间
### 举个栗子
```sql
-- 单表查询
SELECT employee_id, salary, commission_pct, job_id
FROM employees
WHERE job_id = 'SA_REP'
FOR UPDATE
ORDER BY employee_id;
-- 多表查询
SELECT e.employee_id, e.salary, e.commission_pct
FROM employees e JOIN departments d
USING (department_id)
WHERE job_id = 'ST_CLERK'
AND location_id = 1500
FOR UPDATE
ORDER BY e.employee_id;
-- 等待时间
SELECT employee_id, salary, commission_pct, job_id
FROM employees
WHERE job_id = 'SA_REP'
FOR UPDATE WAIT 5
ORDER BY employee_id;
```
