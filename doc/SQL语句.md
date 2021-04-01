#### 基本语法 

    创建表：
        CREATE TABLE `user` (
          `id` INT AUTO_INCREMENT,
          `name` VARCHAR (20),
          PRIMARY KEY (`id`)
        );
    UPDATE:
      UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值
    INSERT: 
      INSERT INTO 表名称 VALUES (值1, 值2,....)
      INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)
    DELETE:
      DELETE FROM 表名称 WHERE 列名称 = 值
    修改表结构：
        ALTER TABLE table_name add column_name datatype
        ALTER TABLE table_name drop COLUMN column_name
        ALTER TABLE table_name modify COLUMN column_name datatype
* INNER JOIN（内连接，或等值连接）：取得两个表中存在连接匹配关系的记录。
        
        select * from A inner join B on A.name = B.name;
* LEFT JOIN（左连接）：取得左表（table1）完全记录，即是右表（table2）并无对应匹配记录。

        select * from A left join B on A.name = B.name;
        select * from A left join B on A.name=B.name where A.id is null or B.id is null;
* RIGHT JOIN（右连接）：与LEFT JOIN 相反，取得右表（table2）完全记录，即是左表（table1）并无匹配对应记录。
      
        SELECT * FROM A LEFT JOIN B ON A.name = B.name
        WHERE B.id IS NULL
        union
        SELECT * FROM A right JOIN B ON A.name = B.name
        WHERE A.id IS NULL;
        
        union ：用于合并多个 select 语句的结果集，并去掉重复的值。 union all ：作用和 union 类似，但不会去掉重复的值
#### Sql优化方案
* 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where及order by涉及的列上建立索引
* 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描 例如:select id from t where num is null
* 应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描。
* 应尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描
* 尽可能的使用 varchar代替char varchar是可变长度，char是不可变长度。 
* 任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段
* union all只是合并查询结果，并不会进行去重和排序操作，在没有去重的前提下，使用union all的执行效率要比union高
