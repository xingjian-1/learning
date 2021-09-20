##### 数据库范式
设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。目前数据库有六种范式：第一范式、第二范式、第三范式、巴斯-科德范式、第四范式、第五范式（又称完美范式）。
##### 三大范式
* 第一范式：列不可再分，具有原子性，即数据库表的每一列都是不可分割的原子数据项，不能是集合、数组等非原子数据项。
* 第二范式：在满足第一范式的前提下，每一列都依赖于主键，表中的每一个字段都依赖于主键。
* 第三范式：在满足第二范式的前提下，每一列都直接依赖于主键，而不能通过其他列来间接的依赖主键。
三大范式只是一般设计数据库的基本理念，可以建立冗余较小、结构合理的数据库。如果有特殊情况，当然要特殊对待，数据库设计最重要的是看需求跟性能，需求>性能>表结构。所以不能一味的去追求范式建立数据库。
##### 表与表的关系分类
* 一对一
* 一对多
* 多对多
#### 字段类型相关
表字段是用来存储数据内容大小的，其大小的设置跟存储到磁盘分配的磁盘块多少有直接关系。换算单位：1KB=1024B，1B=8bit(位)，数据存储是以二进制存储的，一个子节占8位，比如十进制数9大小占一个子节，用二进制表示为：0000 1001。查看数据库表结构文件存放地址执行：show global variables like "%datadir%"。
* char(n) ：固定长度类型，char(10)，当你输入"abc"三个字符的时候，它们占的空间还是10个字节，其他7个是空字节。其优点是存取数据效率高，缺点是占用空间大，以空间换时间，适用场景：存储密码的md5值，固定长度的，使用char非常合适。
* varchar(n) ：可变长度，存储的值是每个值占用的字节再加上一个用来记录其长度的字节的长度。从空间上考虑varcahr比较合适；从效率上考虑char比较合适。
* float(m,d)：单精度浮点数， m显示长度，d表示小数点位数，占用空间4个字节。例如：float(4,2) 表示的范围是-99.99~99.99。
* decimal(m,d):m显示长度，d表示小数点位数。例如：decimal(5,2) 表示范围：-999.99 ~ 999.99。往数据库字段类型为float(10.8)、decimal(10,8)分别插入数据10.12345678，10.12345678，然后执行select * from 表,会发现float类型的字段显示数据为10.12345695，而decimal字段显示的数据为10.12345678，float单精度小数位数小于decimal，decimal如果不指定精度，默认为（10，0）。
* double：浮点类型有两种，分别是单精度浮点数（float）和双精度浮点数（double），定点类型只有一种，就是decimal，可以存储16位的十进制数，占空间8字节。
           
           单精度和双精度的区别：
          1.内存中占有的字节数不同：单精度浮点数在机内占4个字节，双精度浮点数在机内占8个字节。
          2.有效数字位数不同：单精度浮点数有效数字8位，双精度浮点数有效数字16位。
          3.所能表示数的范围不同：单精度浮点的表示范围：-3.40E+38 ~ +3.40E+38，双精度浮点的表示范围：-1.79E+308 ~ +1.79E+308。
          4.在程序中处理速度不同：一般CPU处理单精度浮点数的速度比处理双精度浮点数快。
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
* DELETE对数据一行一行的删除，将每行的删除操作记录在日志中保存，以便进行回滚操作。
* TRUNCATE一次性地从表中删除所有的数据不记录操作日志，数据不能恢复，删除的过程中不会激活与表有关的删除触发器。执行速度快。
* drop语句将表所占用的空间全释放掉。TRUNCATE和DELETE只删除数据， DROP则删除整个表（结构和数据）。
##### 硬件层面优化

            1.CPU（运算）：48核CPU。
            2.内存：96G-256G，跑3-4个实例。
            3.disk(磁盘IO)：机械盘：选SAS，数量越多越好。性能：SSD（高并发） > SAS（普通业务线上） >SATA（线下）
            选SSD：使用SSD或者PCIe SSD设备，可提升上千倍的IOPS效率。随机IO：SAS单盘能力300IOPS SSD随机IO：单盘能力可达35000IOPS Flashcache HBA卡 
            4.raid磁盘阵列：4快盘：RAID0>RAID1(推荐)>RAID5(少用)>RAID1。主库选择raid10，从库可选raid5/raid0/raid10，从库配置等于或大于主库
            5.网卡：使用多块网卡bond，以及buffer，tcp优化。千兆网卡及千兆、万兆交换机
            数据库属于IO密集型服务，硬件尽量不要使用虚拟化
##### 操作系统层面优化

            1.选择x86_64系统，推荐使用CentOS6.8 linux，关闭NUMA特性
            2.将操作系统和数据分开（逻辑上和物理上）
            3.避免使用Swap交换分区
            4.避免使用软件磁盘阵列
            5.避免使用LVM逻辑卷
            6.删除服务器上未使用的安装包和守护进程
##### 网络优化

            #优化系统套接字缓冲区
            #Increase TCP max buffer size
            net.core.rmem_max=16777216 #最大socket读buffer
            net.core.wmem_max=16777216 #最大socket写buffer
            net.core.wmem_default = 8388608 #该文件指定了接收套接字缓冲区大小的缺省值(以字节为单位)
            net.core.rmem_default = 8388608
            #优化TCP接收/发送缓冲区

            # Increase Linux autotuning TCPbuffer limits
            net.ipv4.tcp_rmem=4096 87380 16777216
            net.ipv4.tcp_wmem=4096 65536 16777216
            net.ipv4.tcp_mem = 94500000 915000000927000000
            #优化网络设备接收队列
            net.core.netdev_max_backlog=3000
##### mysql数据库层面优化
* my.cnf参数优化
此优化主要针对innodb引擎，default-storage-engine=Innodb，

            innodb_buffer_pool_size:指定用于索引的缓冲区大小
            innodb_file_per_table = 1：使用独立表空间
            innodb_data_file_path = ibdata1:1G:autoextend,不要使用默认的10%
            innodb_log_file_size=256M,设置innodb_log_files_in_group=2，基本可满足90%以上的场景；
            innodb_log_file_size
            long_query_time = 1记录那些执行较慢的SQL，用于后续的分析排查；
            max_connection（最大连接数）max_connection_error（最大错误数，建议设置为10万以上，而open_files_limit、innodb_open_files、table_open_cache、table_definition_cache这几个参             数则可设为约10倍于max_connection的大小；）不要设置太大，会将数据库撑爆 
            tmp_table_size = 64M ：设置内存临时表最大值。如果超过该值，则会将临时表写入磁盘，其范围1KB到4GB。
            max_heap_table_size = 64M：独立的内存表所允许的最大容量。
            table_cache = 614：给经常访问的表分配的内存，物理内存越大，设置就越大。调大这个值，一般情况下可以降低磁盘IO，但相应的会占用更多的内存,这里设置为614。
* 关于库表的设计规范
            1.推荐utf-8字符集。
            2.固定字符串的列尽可能多用定长char，少用varchar。
            3.所有的InnoDB表都设计一个无业务用途的自增列做主键。
            4.字段长度满足需求前提下，尽可能选择长度小的。
            5.字段属性尽量都加NOT NULL约束（空的字段不能走索引，查询速度慢）对于某些文本字段，例如“省份”或者“性别”我们可以将他们定义为ENUM类型
            6.尽可能不使用TEXT/BLOB类型，确实需要的话，建议拆分到子表中，不要和主表放在一起，避免SELECT*的时候读性能太差。
            7.读取数据时，只选取所需要的列，不要每次都SELECT * 尤其是读到一些TEXT/BLOB类型，确实需要的话，建议拆分到子表中，不要和主表放在一起，避免SELECT*的时候读性能太差
            8.对一个VARCHAR（N）列创建索引时，通常取其50%（甚至更小）左右长度创建前缀索引就足以满足80%以上的查询需求了，没必要创建整列的全长度索引。
            9.多用复合索引，少用多个独立索引。
* sql语句的优化
            索引的优化：
            DBA参与，抓出慢SQL,减少上线后的慢SQL数据,配置my.cnf
            long_query_time = 2
            log-slow-queries=/data/3306/slow-log.log
            log_queries_not_using_indexs
            按天轮询：slow-log.log
            慢查询的日志分析工具——mysqlsla或pt-query-digest（推荐）
            pt-quey-diges,mysqldumpslow,mysqlsla,myprofi,mysql-explain-slow-log,mysqllogfileter
            3)每天晚上0点定时分析慢查询，发到核心开发，DBA分析，及高级运维，CTO的邮箱里
            DBA分析给出优化建议-->核心开发确认更新-->DBA线上操作处理
            4)定期使用pt-duplicate-key-checker检查并删除重复的索引
            定期使用pt-index-usage工具检查并删除使用频率很低的索引
            5)使用pt-online-schema-change来完成大表的ONLINE DDL需求
            6)有时候MySQL会使用错误的索引，对于这种情况使用USE INDEX
            7)使用explain及set profile优化SQL语句
            
            大的复杂的sql语句拆分成多个小的查询语句：
            子查询，JOIN连表查询，某个表4000万条记录。
            
            数据库是存储数据的地方，不是计算数据的地方：
            对于数据计算、各种应用的逻辑处理放到对应的模块解决。
            
            使用连接(JOIN)来代替子查询（Sub_Queries）
            避免在整个表上使用cout(*)，它可能锁住整张表
            多表联接查询时，关联字段类型尽量一致，并且都要有索引。
            在WHERE子句中使用UNION代替子查询
            多表连接查询时，把结果集小的表（注意，这里是指过滤后的结果集，不一样是全表数据量小的）作为驱动表
            对查询进行优化，应尽量避免全表扫描，首先应考虑在 where及order by涉及的列上建立索引
            应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描 例如:select id from t where num is null
            应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描。
            应尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描
            尽可能的使用 varchar代替char varchar是可变长度，char是不可变长度。 
            任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段
            union all只是合并查询结果，并不会进行去重和排序操作，在没有去重的前提下，使用union all的执行效率要比union高
