
#### 数据库的三范式
* 第一范式：列不可再分。确保每一列的原子性
* 第二范式：属性完全依赖于主键。
* 第三范式：属性不依赖于其它非主属性，属性直接依赖于主键。
三大范式只是一般设计数据库的基本理念，可以建立冗余较小、结构合理的数据库。如果有特殊情况，当然要特殊对待，数据库设计最重要的是看需求跟性能，
需求>性能>表结构。所以不能一味的去追求范式建立数据库

#### char和varchar的区别 float和double的区别
* char(n) ：固定长度类型，char(10)，当你输入"abc"三个字符的时候，它们占的空间还是10个字节，其他7个是空字节。优点：效率高；缺点：占用空间；适用场景：存储密码的md5值，固定长度的，使用char非常合适。
* varchar(n) ：可变长度，存储的值是每个值占用的字节再加上一个用来记录其长度的字节的长度。从空间上考虑varcahr比较合适；从效率上考虑char比较合适。
* float 最多可以存储8位的十进制数，并在内存中占4字节。
* double 最可可以存储16位的十进制数，并在内存中占8字节。

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
#### drop、truncate和delete的区别
* DELETE语句执行删除的过程是每次从表中删除一行，并且同时将该行的删除操作作为事务记录在日志中保存以便进行进行回滚操作。
* TRUNCATE TABLE 则一次性地从表中删除所有的数据并不把单独的删除操作记录记入日志保存，删除行是不能恢复的。并且在删除的过程中不会激活与表有关的删除触发器。执行速度快。
* drop语句将表所占用的空间全释放掉。TRUNCATE 和DELETE只删除数据， DROP则删除整个表（结构和数据）。
#### Sql优化方案
* 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where及order by涉及的列上建立索引
* 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描 例如:select id from t where num is null
* 应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描。
* 应尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描
* 尽可能的使用 varchar代替char varchar是可变长度，char是不可变长度。 
* 任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段
* union all只是合并查询结果，并不会进行去重和排序操作，在没有去重的前提下，使用union all的执行效率要比union高
