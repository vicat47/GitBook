# 数据定义语言
**数据定义语言(Data Definition Language,DDL)** 是SQL语言集中负责数据结构定义与数据库对象定义的语言，其语句包括动词CREATE和DROP。在数据库中创建新表或删除表(`CREAT TABLE` 或 `DROP TABLE`);为表加入索引等。DDL包括许多与人数据库目录中获得数据有关的保留字。它也是动作查询的一部分。
## 数据库对象
对象  | 描述
-----|----
TABELE|基本的存储单元，由行和列组成
View|逻辑上代表一个或多个表中数据的子集
Sequence|数据值生成器
Index|提高某些查询的性能
Synonym|给出对象的替代名称


## TRUNCATE 语句(**DANGER**)
### 作用
- 删除表中所有行，留下一个空表，表结构完好无损
- 这是一个数据定义语言 (DDL) 而不是一个 DML语言; **不能轻易的撤消**。
- 使用TRUNCATE语句快速删除表或群集中的所有行。
- 使用TRUNCATE语句删除行比使用DELETE语句删除行更快，原因如下：
  - TRUNCATE语句是数据定义语言（DDL）语句，不生成回滚信息。
  - 截断表不会触发表的删除触发器。
- 如果表是引用完整性约束的父级，则不能截断表。需要在发出TRUNCATE语句之前禁用约束。
### 语法
```sql
TRUNCATE TABLE table_name;
```
### 举个栗子
```sql
TRUNCATE TABLE userinfo;
```
