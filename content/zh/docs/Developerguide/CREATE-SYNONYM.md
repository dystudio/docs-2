# CREATE SYNONYM<a name="ZH-CN_TOPIC_0251556711"></a>

## 功能描述<a name="zh-cn_topic_0237122116_zh-cn_topic_0059778169_s0867185fef0f4a228532d432b598cb26"></a>

创建一个同义词对象。同义词是数据库对象的别名，用于记录与其他数据库对象名间的映射关系，用户可以使用同义词访问关联的数据库对象。

## 注意事项<a name="zh-cn_topic_0237122116_section93413620440"></a>

-   定义同义词的用户成为其所有者。
-   若指定模式名称，则同义词在指定模式中创建。否则，在当前模式创建。
-   支持通过同义词访问的数据库对象包括：表、视图、函数和存储过程。
-   使用同义词时，用户需要具有对关联对象的相应权限。
-   支持使用同义词的DML语句包括：SELECT、INSERT、UPDATE、DELETE、EXPLAIN、CALL。
-   不支持关联函数或存储过程的CREATE SYNONYM语句出现在存储过程中，建议存储过程中使用系统表pg\_synonym中已存在的同义词对象。

## 语法格式<a name="zh-cn_topic_0237122116_zh-cn_topic_0059777835_sebcad83e099e46b0ba586829e634d144"></a>

```
CREATE [ OR REPLACE ] SYNONYM synonym_name 
    FOR object_name;
```

## 参数说明<a name="zh-cn_topic_0237122116_section1549681213574"></a>

-   **synonym**

    创建的同义词名字，可以带模式名。

    取值范围：字符串，要符合标识符的命名规范。

-   **object\_name**

    关联的对象名字，可以带模式名。

    取值范围：字符串，要符合标识符的命名规范。

    >![](public_sys-resources/icon-note.gif) **说明：**   
    >object\_name可以是不存在的对象名称。  


## 示例<a name="zh-cn_topic_0237122116_section1853433744413"></a>

```
--创建模式ot。
postgres=# CREATE SCHEMA ot;

--创建表ot.t1及其同义词t1。
postgres=# CREATE TABLE ot.t1(id int, name varchar2(10));
postgres=# CREATE OR REPLACE SYNONYM t1 FOR ot.t1;

--使用同义词t1。
postgres=# SELECT * FROM t1;
postgres=# INSERT INTO t1 VALUES (1, 'ada'), (2, 'bob');
postgres=# UPDATE t1 SET t1.name = 'cici' WHERE t1.id = 2;

--创建同义词v1及其关联视图ot.v_t1。
postgres=# CREATE SYNONYM v1 FOR ot.v_t1;
postgres=# CREATE VIEW ot.v_t1 AS SELECT * FROM ot.t1;

--使用同义词v1。
postgres=# SELECT * FROM v1;

--创建重载函数ot.add及其同义词add。
postgres=# CREATE OR REPLACE FUNCTION ot.add(a integer, b integer) RETURNS integer AS
$$
SELECT $1 + $2
$$
LANGUAGE sql;

postgres=# CREATE OR REPLACE FUNCTION ot.add(a decimal(5,2), b decimal(5,2)) RETURNS decimal(5,2) AS
$$
SELECT $1 + $2
$$
LANGUAGE sql;

postgres=# CREATE OR REPLACE SYNONYM add FOR ot.add;

--使用同义词add。
postgres=# SELECT add(1,2);
postgres=# SELECT add(1.2,2.3);

--创建存储过程ot.register及其同义词register。
postgres=# CREATE PROCEDURE ot.register(n_id integer, n_name varchar2(10))
SECURITY INVOKER
AS
BEGIN
    INSERT INTO ot.t1 VALUES(n_id, n_name);
END;
/

postgres=# CREATE OR REPLACE SYNONYM register FOR ot.register;

--使用同义词register，调用存储过程。
postgres=# CALL register(3,'mia');

--删除同义词。
postgres=# DROP SYNONYM t1;
postgres=# DROP SYNONYM IF EXISTS v1;
postgres=# DROP SYNONYM IF EXISTS add;
postgres=# DROP SYNONYM register;
postgres=# DROP SCHEMA ot CASCADE;
```

## 相关链接<a name="zh-cn_topic_0237122116_zh-cn_topic_0059778825_section184942174514"></a>

[ALTER SYNONYM](ALTER-SYNONYM.md)，[DROP SYNONYM](DROP-SYNONYM.md)

