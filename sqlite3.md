# sqlite学习记录

## 创建一个数据库
```
sqlite3 #命令行输入，进入sqlite
.open test.db #打开test.db数据库，没有就创建
.databases #查看数据位置
.quit #退出
.help #查看帮助
```
## 创建数据表以及增删改查数据
```
1. 数据类型
NULL	值是一个 NULL 值。
INTEGER	值是一个带符号的整数，根据值的大小存储在 1、2、3、4、6 或 8 字节中。
REAL	值是一个浮点值，存储为 8 字节的 IEEE 浮点数字。
TEXT	值是一个文本字符串，使用数据库编码（UTF-8、UTF-16BE 或 UTF-16LE）存储。
BLOB	值是一个 blob 数据，完全根据它的输入存储。
ref: https://www.runoob.com/sqlite/sqlite-data-types.html

2. 创建表
CREATE TABLE FP_DB(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   DB_ID          TEXT    NOT NULL,
   CREATE_DATETIME   TEXT
);

.tables # 查看
.schema fp_db
.header on 显示表头
.width 10, 20, 10 依次设置列宽

3. 插入数据
INSERT INTO FP_DB (ID,NAME,DB_ID,CREATE_DATETIME)
VALUES (1, 'test', '123456', '' );

INSERT INTO FP_DB VALUES (2, 'test2', '1234567', '' );

4. 删除数据
DELETE FROM FP_DB WHERE ID = 1;
DELETE FROM FP_DB; //删除表所有数据

5. 修改数据
UPDATE FP_DB SET DB_ID = '678910' WHERE NAME = 'test2';
 
6. 查询数据
SELECT * FROM FP_DB;
SELECT ID, NAME,DB_ID FROM FP_DB;

#---------------------------------------------------------------------------------------------------------------------
sqlite_master表：
sqlite_master是一个表，属于系统表，存放在根页中，每一个数据库的.db文件都有一个sqlite_master表。
sqlite_master表存放了.db中所有表的相关信息，比如:表的名称、用于创建此表的sql语句、索引、索引所属的表、创建索引的sql语句等。
sqlite_master表是只读的，只能对他进行读操作，写的操作由系统自动执行，使用者没有写的执行权限。用户对自定义表的操作多会自动同步到这个表中。

sqlite_master结构：

sqlite_master(
type TEXT, 
name TEXT, 
tbl_name TEXT, 
rootpage INTEGER, 
sql TEXT 
); 
TEXT——：text永远默认存储的的table，表类的字段
name——：name存放表名的字段，可以输入要查询的表名

使用示例：
1.查询表存在否：SELECT * from sqlite_master where name = '表名'
2.查询表中的字段存在否：SELECT * from sqlite_master where name = '表名' and sql like '%字段%'（sql like与通配符作用相似，查询包含%%中间的字段）
3.2.查询表中的字段存在否（具体）：SELECT * from sqlite_master where name = '表名' and sql = '字段'
ref: https://blog.csdn.net/afootball/article/details/108200419
#--------------
ELECT sql FROM sqlite_master WHERE type = 'table' AND tbl_name = 'FP_DB';
```

## python3操作sqlite

### 1. 数据库创建
```
import sqlite3
conn = sqlite3.connect('test.db')
print("数据库打开成功")
```

### 2. 创建数据表
```
import sqlite3

conn = sqlite3.connect('test.db')
print("数据库打开成功")
c = conn.cursor()
sql_1 = """
CREATE TABLE FP_DB(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   DB_ID          TEXT    NOT NULL,
   CREATE_DATETIME   TEXT
);
"""
c.execute(sql_1)
print("数据表创建成功")
conn.commit()
conn.close()
```
### 3. 插入数据
```
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
print("数据库打开成功")

c.execute("INSERT INTO FP_DB (ID,NAME,DB_ID,CREATE_DATETIME) \
VALUES (1, 'test', '123456', '' );")
      
c.execute("INSERT INTO FP_DB VALUES (2, 'test2', '1234567', '' );")

conn.commit()
print("数据插入成功")
conn.close()
```
### 4. 删除数据
```
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
print("数据库打开成功")

c.execute("DELETE FROM FP_DB WHERE ID = 1;")
conn.commit()
print "Total number of rows deleted :", conn.total_changes

cursor = c.execute("SELECT ID,NAME,DB_ID,CREATE_DATETIME FROM FP_DB")
for row in cursor:
   print("ID = ", row[0])
   print("NAME = ", row[1])
   print("DB_ID = ", row[2])
   print("CREATE_DATETIME = ", row[3])

print("数据操作成功")
conn.close()
```
### 5. 修改数据
```
#!/usr/bin/python

import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
print("数据库打开成功")

c.execute("UPDATE FP_DB SET DB_ID = '678910' WHERE NAME = 'test2';")
conn.commit()
print("Total number of rows updated :", conn.total_changes)

cursor = c.execute("SELECT ID,NAME,DB_ID,CREATE_DATETIME FROM FP_DB")
for row in cursor:
   print("ID = ", row[0])
   print("NAME = ", row[1])
   print("DB_ID = ", row[2])
   print("CREATE_DATETIME = ", row[3])

print("数据操作成功")
conn.close()
```
### 6. 查询数据
```
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
print("数据库打开成功")

cursor = c.execute("SELECT ID,NAME,DB_ID,CREATE_DATETIME FROM FP_DB")
for row in cursor:
   print("ID = ", row[0])
   print("NAME = ", row[1])
   print("DB_ID = ", row[2])
   print("CREATE_DATETIME = ", row[3])

print("数据操作成功")
conn.close()
```
