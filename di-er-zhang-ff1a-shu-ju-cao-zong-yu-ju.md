# 第二章:数据操纵语句
**数据操纵语句(Data Manipulation Language,DML)** 是SQL的核心部分，当你想要添加，更新或删除数据时，你需要执行一个DML语句。形成逻辑工作单元的DML语句的集合称为 **事务**
## INSERT 语句
### 作用
向表中添加新行
### 语法
#### 通过 INSERT INTO 表名 VALUES (数据)插入
```sql
INSERT INTO	table [(column [, column...])]
VALUES (value [, value...]);
```
- 使用此语法，一次只能插入一行.
#### 通过 子查询 插入
```sql
INSERT INTO	table [(column [, column...])]
      SELECT [(column [, column...])]
      FROM table;
```
- 通过子查询插入可以**一次性插入多行**查询到的数据。
- **不能**使用VALUES子句
- 使INSERT子句中的列数与子查询中的列数匹配
- 会将子查询得到的结果集**全部插入**.
### 插入新行
- **如果不指定插入数据列的列名，必须给定足够的值**，如果给定的参数不够的话，会报`ORA-00947: 没有足够的值`错误。
- 也可以指定要插入的列的列名
- **必须按照表中默认列出值的顺序插入数据**
- 字符和日期类型包含在单引号中，不建议将数值加入单引号中
### 插入空值
通过使用`DESCRIBE`命令验证Null状态，确保可以在目标列中使用空值。如果该列有 *非空约束* ，**必须** 将非空列填充，否则会报 `ORA-01400: 无法将 NULL 插入` 错误。
-  Oracle服务器自动强制执行所有数据类型，数据范围和数据完整性约束。 未列出的任何列明确获取新行中的空值。
- 按照以下顺序检查在用户输入过程中可能发生的常见错误：
- NOT NULL列缺少强制值
- 重复的值违反任何唯一键或主键约束
- 任何违反CHECK约束的值
- 维护外键约束的参照完整性
- 数据类型不匹配或值太大而不适合列
注意：建议使用列列表，因为它使INSERT语句更可读和可靠，或者更不容易出错。
### 举个栗子
```sql
--1.在表名后边不显式的声明列，按照表结构中列的数量、顺序、数据类型往表中插入数据
--insert into 表名 values(列1,列2,列3....);
INSERT INTO userinfo VALUES
      ('qwe','asd',NULL,NULL,NULL);
--2.在表名后边显示声明要插入的列，没有声明的那些列会插入null值
INSERT INTO userinfo (username,password,birthday)
      VALUES ('test1','test1',SYSDATE);
--3.使用子查询插入数据
INSERT INTO userinfo
      SELECT last_name, first_name, hire_date, 0, phone_number
      FROM employees WHERE salary > 2000;
INSERT INTO userinfo (username, password)
      SELECT last_name, first_name
      FROM employees WHERE salary > 50;
```
## UPDATE 语句
### 作用
UPDATE 语句用于修改表中的数据。
### 语法
#### 通过 UPDATE table SET 修改行
```sql
UPDATE		table
SET		column = value [, column = value, ...]
[WHERE 		condition];
```
- 如果指定了WHERE子句，则可以修改某特定的一行或多行
- 如果省略了WHERE子句，则会修改**整个列**的值
- 使用SET column_name= NULL 可以将一列的值更改为NULL值。
#### 通过 子查询 修改行
```sql
UPDATE table
SET    column  = (SELECT	column
                  FROM table
                  WHERE condition)
       [ ,
       column  = (SELECT	column
                  FROM table
                  WHERE condition)]
[WHERE  condition ];
```
