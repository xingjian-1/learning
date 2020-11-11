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
