# 数据控制语言
**数据控制语言(Data Control Language,DCL)** 是用来设置或者更改数据库用户或角色权限的语句，这些语句包括`GRANT`、`DENY`、`REVOKE`等语句，在默认状态下， **只有sysadmin、dbcreator、db_owner或db_securityadmin等角色的成员才有权利执行数据控制语言**。
## 表空间
### 作用
- 存储表（数据）的空间，可以客观的在操作系统里看到表空间对应的物理文件。
### 数据库的物理及逻辑结构
![](https://ws1.sinaimg.cn/large/e50fdd0bly1fhqmai8ahgj20cp097dfz.jpg)
### 创建表空间
- 指定表空间的名称，大小，以及对应的操作系统上的物理文件
```sql
CREATE TABLESPACE tbsname DATAFILE '操作系统上对应的物理文件的绝对路径' SIZE nM|G;
Select 临时表空间
存放临时数据的特殊表空间
CREATE TEMPORARY TABLESPACE tbs tbsname TEMPFILE '操作系统上对应的物理文件的绝对路径' SIZE nM|G;
```
## 用户
- 用户名命名方式类似表和列的命名
- 用户的一些属性（dba_users）
  - 用户名
	- 密码
	- 账户状态
	- 创建日期
	- 默认的表空间
	- 默认的临时表空间
### 创建用户
#### 语法
```sql
CREATE USER username IDENTIFIED BY password [DEFAULT TABLESPACE tbsname TEMPORARY TABLESPACE tbsname];
```
- 创建用户时，可以指定该用户的默认表空间以及默认临时表空间
## 权限
- 常用权限
	- 使用表空间的权限
	- 连接数据库的权限
	- 创建基本的对象的权限（表、视图、索引、序列、约束、同义词）
	- 其它权限
- 授权与撤销权限（DCL）
	- grant
	- revoke
### 角色
- 一组权限的集合
  - resource,connect
  - role_sys_privs 视图
  - role_tab_privs视图
`grant connect,resource to user;`
执行上面的sql语句后用户包括的权限:
- CONNECT角色： --是授予最终用户的典型权利，最基本的
  - ALTER SESSION --修改会话
  - CREATE CLUSTER --建立聚簇
  - CREATE DATABASE LINK --建立数据库链接
  - CREATE SEQUENCE --建立序列
  - CREATE SESSION --建立会话
  - CREATE SYNONYM --建立同义词
  - CREATE VIEW --建立视图
  - RESOURCE角色： --是授予开发人员的
  - CREATE CLUSTER --建立聚簇
  - CREATE PROCEDURE --建立过程
  - CREATE SEQUENCE --建立序列
  - CREATE TABLE --建表
  - CREATE TRIGGER --建立触发器
  - CREATE TYPE --建立类型
### 授权
```sql
GRANT privname TO username;
```
### 撤销
```sql
REVOKE privname FROM username;
```
