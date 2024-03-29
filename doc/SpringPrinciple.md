#### 框架概况
Spring框架是Java平台上的一种开源应用框架，提供具有控制反转特性的容器。尽管Spring框架自身对编程模型没有限制，但其在Java应用中的频繁使用让它备受青睐，以至于后来让它作为EJB（EnterpriseJavaBeans）模型的补充，甚至是替补。Spring框架为开发提供了一系列的解决方案，比如利用控制反转的核心特性，并通过依赖注入实现控制反转来实现管理对象生命周期容器化，利用面向切面编程进行声明式的事务管理，整合多种持久化技术管理数据访问，提供大量优秀的Web框架方便开发等等。Spring框架具有控制反转（IOC）特性，IOC旨在方便项目维护和测试，它提供了一种通过Java的反射机制对Java对象进行统一的配置和管理的方法。Spring框架利用容器管理对象的生命周期，容器可以通过扫描XML文件或类上特定Java注解来配置对象，开发者可以通过依赖查找或依赖注入来获得对象。Spring框架具有面向切面编程（AOP）框架，SpringAOP框架基于代理模式，同时运行时可配置；AOP框架主要针对模块之间的交叉关注点进行模块化。Spring框架的AOP框架仅提供基本的AOP特性，虽无法与AspectJ框架相比，但通过与AspectJ的集成，也可以满足基本需求。Spring框架下的事务管理、远程访问等功能均可以通过使用SpringAOP技术实现。Spring的事务管理框架为Java平台带来了一种抽象机制，使本地和全局事务以及嵌套事务能够与保存点一起工作，并且几乎可以在Java平台的任何环境中工作。Spring集成多种事务模板，系统可以通过事务模板、XML或Java注解进行事务配置，并且事务框架集成了消息传递和缓存等功能。Spring的数据访问框架解决了开发人员在应用程序中使用数据库时遇到的常见困难。它不仅对Java:JDBC、iBATS/MyBATIs、Hibernate、Java数据对象（JDO）、ApacheOJB和ApacheCayne等所有流行的数据访问框架中提供支持，同时还可以与Spring的事务管理一起使用，为数据访问提供了灵活的抽象。Spring框架最初是没有打算构建一个自己的WebMVC框架，其开发人员在开发过程中认为现有的StrutsWeb框架的呈现层和请求处理层之间以及请求处理层和模型之间的分离不够，于是创建了SpringMVC。
            
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
* Spring5又做了一些改变.............
#### 框架原理示例
* 创建一个Person类：

                    public class Person {
                        private String name;
                        private int age;
                        private Book book;
                        public String getName() {
                            return name;
                        }
                        public void setName(String name) {
                            this.name = name;
                        }
                        public int getAge() {
                            return age;
                        }
                        public void setAge(int age) {
                            this.age = age;
                        }
                        public Book getBook() {
                            return book;
                        }
                        public void setBook(Book book) {
                            this.book = book;
                        }
* 创建一个book类：

                    public class Book {
                        private String name;
                        private int price;
                        private String place;
                        public String getName() {
                            return name;
                        }
                        public void setName(String name) {
                            this.name = name;
                        }
                        public int getPrice() {
                            return price;
                        }
                        public void setPrice(int price) {
                            this.price = price;
                        }
                        public String getPlace() {
                            return place;
                        }
                        public void setPlace(String place) {
                            this.place = place;
                        }
                      }
* applicationContext.xml配置文件:

                    <?xml version="1.0" encoding="UTF-8"?>
                    <beans xmlns="http://www.springframework.org/schema/beans"
                        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
                    <!-- bean的配置文件 -->
                    <bean id="person" class="org.jingdong.bean.life.Person">
                    <property name="name" value="grl"></property>
                    <property name="age" value="11"></property> 
                    <property name="book" ref="book"></property></bean>
                    
                    <bean id="book" class="org.jingdong.bean.life.Book">
                    <property name="name" value="think in java"></property>
                    <property name="place" value="USA"></property>
                    <property name="price" value="79"></property>
                    </beans>
* main方法

                       public class Main {
                              public static void main(String[] args) {
                                  //创建IOC容器
                                  ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
                                  //从容器中获取bean实例
                                  Person person = (Person) ac.getBean("person");
                                  //使用bean
                                  System.out.println(person.getName());
                                   }
                                 }
                    
 * 运行原理解析：
 ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext .xml")被执行时，Spring容器对象被创建，同时applicationContext.xml中的bean会被加载到内存中。
 
                    BeanFactory和ApplicationContext区别
                    1.ApplicationContext：加载xml配置文件时，配置文件中的配置的bean已经被实例化<br>
                    2.BeanFactory：在加载配置文件时，配置文件中的bean不被实例化，只有当通过getBean(),获取bean实例的时候才被创建。<br>
                    总结：通过BeanFactory配置的bean比通过ApplicationContext配置的节约内存。
 * Spring Bean的生命周期
 
                    org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
                    信息: Loading XML bean definitions from class path resource [springBeanTest/beans.xml]
                    这是BeanFactoryPostProcessor实现类构造器！！
                    BeanFactoryPostProcessor调用postProcessBeanFactory方法
                    这是BeanPostProcessor实现类构造器！！
                    这是InstantiationAwareBeanPostProcessorAdapter实现类构造器！！
                     org.springframework.beans.factory.support.DefaultListableBeanFactory preInstantiateSingletons
                    信息: Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@9934d4: defining beans [beanPostProcessor,instantiationAwareBeanPostProcessor,beanFactoryPostProcessor,person]; root of factory hierarchy
                    InstantiationAwareBeanPostProcessor调用postProcessBeforeInstantiation方法
                    【构造器】调用Person的构造器实例化
                    InstantiationAwareBeanPostProcessor调用postProcessPropertyValues方法
                    【注入属性】注入属性address
                    【注入属性】注入属性name
                    【注入属性】注入属性phone
                    【BeanNameAware接口】调用BeanNameAware.setBeanName()
                    【BeanFactoryAware接口】调用BeanFactoryAware.setBeanFactory()
                    BeanPostProcessor接口方法postProcessBeforeInitialization对属性进行更改！
                    【InitializingBean接口】调用InitializingBean.afterPropertiesSet()
                    【init-method】调用<bean>的init-method属性指定的初始化方法
                    BeanPostProcessor接口方法postProcessAfterInitialization对属性进行更改！
                    InstantiationAwareBeanPostProcessor调用postProcessAfterInitialization方法
                    容器初始化成功
                    Person [address=广州, name=张三, phone=110]
                    现在开始关闭容器！
                    【DiposibleBean接口】调用DiposibleBean.destory()
                    【destroy-method】调用<bean>的destroy-method属性指定的初始化方法

###### 在BeanFactory里只对IOC容器的基本行为作了定义，根本不关心你的bean是如何定义怎样加载的。正如我们只关心工厂里得到什么的产品对象，至于工厂是怎么生产这些对象的，这个基本的接口不关心。而要知道工厂是如何产生对象的，我们需要看具体的IOC容器实现，spring提供了许多IOC容器的实现。比如XmlBeanFactory，ClasspathXmlApplicationContext等

               public interface BeanFactory {   
                    //对FactoryBean的转义定义，因为如果使用bean的名字检索FactoryBean得到的对象是工厂生成的对象，    
                    //如果需要得到工厂本身，需要转义           
                    String FACTORY_BEAN_PREFIX = "&"; 
                       
                    //根据bean的名字，获取在IOC容器中得到bean实例    
                    Object getBean(String name) throws BeansException;    
                  
                   //根据bean的名字和Class类型来得到bean实例，增加了类型安全验证机制。    
                    Object getBean(String name, Class requiredType) throws BeansException;    
                   
                   //提供对bean的检索，看看是否在IOC容器有这个名字的bean    
                    boolean containsBean(String name);    
                   
                   //根据bean名字得到bean实例，并同时判断这个bean是不是单例    
                   boolean isSingleton(String name) throws NoSuchBeanDefinitionException;    
                   
                   //得到bean实例的Class类型    
                   Class getType(String name) throws NoSuchBeanDefinitionException;    
                   
                   //得到bean的别名，如果根据别名检索，那么其原名也会被检索出来    
                  String[] getAliases(String name);                
               }
* 类的反射机制
* 接口继承接口，抽象类实现接口，实现类继承抽象类
