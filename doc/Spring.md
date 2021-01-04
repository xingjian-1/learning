#### 概况
Spring是一个开源框架，Spring是于2003 年兴起的一个轻量级的Java 开发框架，由Rod Johnson 在其著作Expert One-On-One J2EE Development and Design中阐述的部分理念和原型衍生而来。它是为了解决企业应用开发的复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许使用者选择使用哪一个组件，同时为 J2EE 应用程序开发提供集成的框架。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。然而，Spring的用途不仅限于服务器端的开发。从简单性、可测试性和松耦合的角度而言，任何Java应用都可以从Spring中受益。Spring的核心是`控制反转`（IoC）和`面向切面`（AOP）。简单来说，Spring是一个`分层`的JavaSE/EE full-stack(一站式) `轻量级``开源`框架。
            
            1.web层：struts，spring-MVC
            2.service层：spring
            3.dao层：hibernate，mybatis，jdbcTemplate

#### 体系结构
Spring框架整体被分为五个模块:`核心容器`(core container), `面向切面编程`(aop), `数据访问`(date access), `Web`, `单元测试`Test.Spring4去掉了spring3中的struts,添加了spring-messaging和spring-websocket,其他模块保持不变
* 核心容器
     
        core部分包含4个模块
        1.spring-core：依赖注入IoC与DI的最基本实现
        2.spring-beans：Bean工厂与bean的装配
        3.spring-context：spring的context上下文即IoC容器 
        4.spring-expression：spring表达式语言

         它们的完整依赖关系：spring-core依赖了commons-logging，而其他模块都依赖了spring-core，所以整个spring框架都依赖了commons-logging，如果项目中有的日志实现如：log4j，可以排除对commons-logging的依赖，没有日志实现而排除了commons-logging依赖，编译报错
* 面向切面编程AOP

        aop部分包含4个模块 
        1.spring-aop：面向切面编程
        2.spring-aspects：集成AspectJ
        3.spring-instrument：提供一些类级的工具支持和ClassLoader级的实现，用于服务器
        4.spring-instrument-tomcat：针对tomcat的instrument实现(包含了spring的tomcat设备代理)
* 数据访问层

         data access部分包含5个模块
          spring-jdbc：jdbc的支持
          spring-tx：事务控制
          spring-orm：对象关系映射，集成orm框架
          spring-oxm：对象xml映射
          spring-jms：java消息服务
* Web

         spring-web：基础web功能，如文件上传
         spring-webmvc：mvc实现
         spring-webmvc-portlet：基于portlet的mvc实现
         spring-websocket：为web应用提供的高效通信工具
* Test

        spring-test：spring测试，提供junit与mock测试功能
        spring-context-support：spring额外支持包，比如邮件服务、视图解析等
* Spring4新增

       Spring4去掉了spring3中的struts,添加了messaging和websocket,其他模块保持不变.
       spring-websocket：为web应用提供的高效通信工具
       spring-messaging：用于构建基于消息的应用程序
       
       现在Spring最新版本是Spring5.官网：https://spring.io/projects/spring-framework
       Mybatis是目前国内Java web开发的主流ORM框架。作为一名开发者非常有必要掌握其实现原理，更好地解决我们开发中遇到的问题；同时，Mybatis的架构和源码非常优雅，使用了大量的设计模式实现解耦以及高扩展性对其设计思想也需要深入理解。
       除了Mybatis之外，与其处在同一重要位置的开源框架还有Spring。比如你是如何理解Spring中的IOC、DI、AOP这些概念的？比如你知道Bean的生命周期吗？说说它有哪些步骤？每个步骤是用来干什么的......
