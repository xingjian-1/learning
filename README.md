###### 由简入繁 从繁至简 
###### 互联网JAVA工程师进阶知识扫盲，涵盖高并发、高可用、分布式、微服务、海量数据处理、金融业务等领域知识 
###### --JAVA高级及资深技术
###### 特别提醒！！！`各知识点描述有插入图片辅助说明，浏览过程当中如果图片显示不出来 请在电脑C:\Windows\System32\drivers\etc 目录下编辑host文件，增加: 199.232.68.133 raw.githubusercontent.com 映射配置`

### `基础`
- [数据结构](./doc/数据结构.md)
    - [数组](./doc/数组.md)  [链表](./doc/链表.md)  [栈](./doc/stack.md)  [队列](./doc/queue.md)  [哈希表](./doc/hash.md)  [树](./doc/tree.md)  [堆]   [图形]
- [算法](./doc/算法.md)
    - [字符串操作类算法](./doc/字符串操作类算法.md)
    - [查找类算法](./doc/查找类算法.md)  
        - [哈希算法](./doc/哈希算法.md) [顺序查找](./doc/顺序查找.md)   [二分查找](./doc/二分查找.md)   [插值查找]   [斐波那契查找]   [树表查找]   [分块查找]
    - [排序类算法](./doc/排序类算法.md)  
        - [冒泡排序](./doc/冒泡排序.md)  [选择排序](./doc/选择排序.md)  [插入法排序](./doc/插入法排序.md)  [计数排序](./doc/计数排序.md)  [堆排序](./doc/堆排序.md)    [快速排序](./doc/快速排序.md)   [归并排序](./doc/归并排序.md)   [桶排序](./doc/桶排序.md)
    - [搜索类算法]
- [设计模式]
    - [创建型] 
        -  [工厂模式](./doc/工厂模式.md)  [单例模式](./doc/单例模式.md)  [原型模式](./doc/原型模式.md)  [建造者模式](./doc/建造者模式.md)
    - [结构型]
        - [代理模式](./doc/抽象工厂模式.md)  [装饰模式](./doc/装饰模式.md)  [适配器模式](./doc/适配器模式.md)   [桥接模式](./doc/桥接模式.md)   [组合模式](./doc/组合模式.md)    [外观模式](./doc/创建型.md)
    - [行为型]
        - [策略模式](./doc/策略模式.md)   [观察者模式](./doc/设计模式.md)   [模板方法模式](./doc/行为型.md)   [状态模式](./doc/结构型.md)
- [多线程](./doc/多线程.md)
    - [多线程实现](./doc/多线程的实现.md)  [线程池创建](./doc/线程池.md)
- [虚拟机](./doc/jvm.md)
    - [JVM内存结构](./doc/JVM内存结构.md)   [JVM垃圾收集策略](./doc/JVM垃圾收集策略.md)   [JVM性能调优](./doc/JVM性能调优.md)   [类文件结构](./doc/类文件结构.md)   [类加载过程](./doc/类加载过程.md)   [类加载器](./doc/类加载器.md)   
- [集合](./doc/Java基础及集合.md)
    - [HashMap底层实现原理](/doc/HashMap底层实现和原理.md)
- [网络](./doc/网络.md)
  - [网络监控](./doc/monitor.md) [socket](./doc/socket.md) [多路复用机制详解](./doc/多路复用机制.md)
- [IO](./doc/IO.md)
- [异常](./doc/异常.md)
### `数据库`
- [MySQL]
    - [事务](./doc/事务.md)  [锁](./doc/悲观锁和乐观锁.md)  [存储引擎](./doc/存储引擎.md)  [索引](./doc/索引.md)  [优化](./doc/SQL语句.md)  [集群](./doc/mysql.md)
- [Oracle](./doc/oracle.md)
### `开源框架`
- [常用框架]
    - [Spring简介](./doc/Spring.md)   [Spring运行原理](./doc/SpringPrinciple.md)   [Spring控制反转](./doc/SpringIOC.md)  [Spring面向切面](./doc/SpringAOP.md)   [Spring配置相关](./doc/SpringSet.md)     [Spring注解](./doc/SpringAutware.md)
    - [Springmvc体系结构](./doc/SpringMVC体系结构.md)   [SpringMVC运行流程](./doc/SpringMVC.md)
    - [SpringBoot体系结构](./doc/SpringBoot.md)    [SpringBoot运行流程](./doc/SpringBoot运行流程.md)
    - [SpringCloud体系结构](./doc/五大核心组件.md)     [SpringCloud运行流程](./doc/SpringCloud介绍.md)   
    - [SpringBoot、SpringCloud集成swagger2](./doc/集成swagger.md)    [SpringBoot、SpringCloud集成Mybatis](./doc/集成Mybatis.md)
    - [Shiro权限管理框架](./doc/Shiro.md)  [Maven](./doc/maven.md) [Motan]  [Dubbo]
    - [Kubernetes](./doc/Kubernetes.md) [Docker](./doc/docker.md)
- [持久层框架]
    - [MyBaits简介](./doc/MyBatis.md)  [Mybaits缓存机制](./doc/IBatis.md)  [Mybaits代码生成工具](./doc/生成工具.md)
    - [Hibernate](./doc/Hibernate.md)
- [版本管理]
    - [分布式版本管理工具Git](./doc/git.md)
    - [SVN]
### `分布式系统`
- [消息队列](./doc/消息队列.md)
    - [为什么要使用消息队列？消息队列有哪些？各有什么优缺点](./doc/消息队列.md)
    - [如何保证消息队列的高可用？](./doc/MQ集群.md)
    - [如何保证消息不被重复消费？(消息消费的幂等性)](./doc/消息幂等性.md)
    - [如何保证消息的可靠性传输？怎么处理消息丢失的问题？](./doc/消息丢失.md)
    - [如何保证消息的顺序性？](./doc/消息消费顺序.md)
    - [如何解决消息队列的延时以及过期失效问题？消息队列满了如何处理？有几百万条数据持续积压几小时，如何解决？](./doc/消息过期失效.md)
    - [如果让你写一个消息队列，如何架构？大致思路。。。]
- [缓存](./doc/缓存.md)
- [分布式服务框架]
- [分布式事务]   [分布式锁](./doc/分布式锁.md)   [分布式会话]
- [分库分表]   [读写分离]
- [搜索引擎]
- [限流]   [熔断]
- [微服务]
    - [SpringCloud五大核心组件](./doc/五大核心组件.md)
### `LINUX相关`
- [linux常用命令](./doc/linux.md)  [sqluldr数据导出工具下载](./doc/sqluldr.zip)  [ksh-64位命令解释器下载](./doc/ksh-20120801-37.el6_9.x86_64.rpm)
### `系统性能`
- [如何提升系统性能]
- [系统性能指标](./doc/性能测试指标.md)
### `海量数据处理`
- [如何从大量的URL中找出相同的URL]
### `对金融业务的理解`
- 理财
- 信贷
- 票据
    - [汇票](./doc/BillOfExchange.md)
        - [贴现]
        - [转贴现]
        - [再贴现]
    - [本票](./doc/PromissoryNotes.md)
    - [支票](./doc/cheque.md)
    - [旅行支票](./doc/TravelerCheque.md)
- 国际结算
    - [进口代收](./doc/ImportCollection.md)
        - [跟单代收]
            - [付款交单]
            - [承兑交单]
        - [光票代收]
    - [出口托收](./doc/ExportCollection.md)
        - [跟单托收](./doc/OutwardCollection.md)
            - [付款交单](./doc/DocumentsAgainstPayment.md)
            - [承兑交单](./doc/DocumentsAgainstAcceptance.md)
        - [光票托收](./doc/CleanCollection.md)
    - [进口信用证](./doc/ImportCredit.md)
    - [出口信用证](./doc/ExportCredit.md)
    - [对外保函]
    - [同业代付](./doc/BankRefinance.md)
    - [汇出汇款](./doc/OutwardRemittance.md)
    - [汇入汇款](./doc/InwardRemittance.md)
